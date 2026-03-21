---
title: "How a European DNS Server Added 1 Second to Every Page Load in Australia"
date: 2026-03-21T13:18:25+05:30
lastmod: 2026-03-21T13:18:25+05:30
draft: false
keywords: ["DNS", "CDN", "GeoDNS", "EDNS Client Subnet", "Bunny CDN", "Rails", "performance", "latency", "Australia"]
description: "A Sydney Rails server was taking 1 second to fetch from a CDN with a Sydney edge. The cause: a European DNS resolver that broke GeoDNS routing, sending traffic through Canada and Germany."
tags: ["devops", "performance", "DNS", "CDN", "rails"]
categories: ["DevOps"]
author: "Harisankar P S"

comment: true
toc: true
autoCollapseToc: false
postMetaInFooter: false
hiddenFromHomePage: false
contentCopyright: false
reward: false
mathjax: false
mathjaxEnableSingleDollar: false
mathjaxEnableAutoNumber: false
hideHeaderAndFooter: false

flowchartDiagrams:
  enable: false
  options: ""

sequenceDiagrams:
  enable: false
  options: ""

---

At the company where I currently work, we run a multi-brand e-commerce platform with stores in around 60 countries per brand. This year we started moving off AWS to self-hosted bare metal servers that we manage ourselves, spread across multiple providers (Hetzner, OVH, and others) for resilience. We have three regions: EU (primary), AU (Sydney), and SG (Singapore). Reads and writes happen locally on the server closest to each user.

I was tasked to investigate our AU server randomly being slower compared to EU. Not always, just sometimes. The same pages that rendered in under 100ms on EU would take over a second on AU.

<!--more-->

## The Symptom

We use [Checkly](https://www.checklyhq.com/) to monitor our services. I have a browser check running every 15 minutes that visits the site from both EU and AU. I started noticing this cycle where the AU check would randomly slow down. When I looked at the individual steps, it was always the product page.

My first thought was that maybe our AU instance was connecting to the primary database in EU instead of the local replica. That would explain the latency. So I pulled up the production logs on the AU server:

```
Completed 200 OK in 1048ms (Views: 1044.7ms | ActiveRecord: 0.9ms)
```

0.9ms in database queries. The DB was fine, definitely hitting the local replica. But 1044ms in view rendering? That didn't make sense. And it was only happening sometimes. When the Rails cache was warm, pages were fast. When a cache entry expired, that page would take a second.

## What I found

Our product descriptions are stored as HTML files on [Bunny Storage](https://bunny.net/storage/) and served through Bunny CDN (`cdn-content.example.io`). When the Rails fragment cache expires, the app makes a synchronous `Net::HTTP.get_response` call to fetch the description HTML from the CDN during view rendering.

So that explained the intermittent part. It was only slow on cache misses. I timed the fetch from the AU server's Rails console:

```ruby
url = 'https://cdn-content.example.io/descriptions/PROD-08422/e5d58...36.html'
start = Time.now
Net::HTTP.get_response(URI(url))
puts "#{((Time.now - start) * 1000).round}ms"
# => 976ms
```

976ms for a CDN fetch. From a server in Sydney. Where Bunny has both an edge node and a storage replica. That didn't add up.

## Checking the CDN headers

Bunny CDN has these useful response headers that tell you which edge and storage node actually served your request. So I ran:

```bash
curl -sI "https://cdn-content.example.io/descriptions/PROD-08422/e5d58...36.html" \
  | grep -i "cdn-cache\|server\|cdn-storageserver"
```

From the Sydney server:

```
server: BunnyCDN-CA1-1025
cdn-storageserver: DE-1141
cdn-cache: HIT
```

From India (I tested from there too for comparison):

```
server: BunnyCDN-CEN1-1045
cdn-storageserver: SYD-788
cdn-cache: HIT
```

So our Sydney server was hitting an edge in Canada (CA1) and pulling content from storage in Germany (DE). A request from India was correctly going to Sydney storage (SYD-788). The request from our AU server was taking this path:

```
Sydney → Canada (edge) → Germany (storage) → Canada → Sydney
```

The content was a cache HIT. The edge had it. But the edge was on the wrong continent entirely.

## The DNS problem

At this point I checked what DNS resolver the AU server was using:

```bash
nslookup cdn-content.example.io
# Server: 127.0.0.53

resolvectl status | grep "DNS Servers"
# DNS Servers: 213.186.33.99
```

`213.186.33.99` is an OVH DNS resolver sitting in Europe. When we moved to bare metal across multiple providers, each provider set their own default DNS resolver on the servers. Our OVH-provisioned AU server just inherited a European one and nobody noticed.

The DNS lookup for the Bunny CDN domain was going all the way to Europe. Whatever mechanism Bunny uses to route requests to the nearest edge, it was seeing a European DNS source and returning a European/Canadian edge. Our Sydney server was invisible to the routing decision.

This also explained why the EU server never had this problem. It was actually close to the European DNS resolver, so it was getting the right edge by coincidence.

## The fix

I needed a DNS resolver that was actually close to our servers. I went with Cloudflare's `1.1.1.1` because I know their DNS infrastructure is distributed globally, with Points of Presence in most major cities including Sydney.

```ini
# /etc/systemd/resolved.conf
[Resolve]
DNS=1.1.1.1 1.0.0.1 213.186.33.99
```

We kept the OVH resolver as the last fallback option. Better to resolve slowly than not at all.

```bash
sudo systemctl restart systemd-resolved
```

After the change, the Bunny CDN domain started resolving to the Sydney edge. I'm honestly not 100% sure of the exact mechanism here, whether it's EDNS Client Subnet, or Bunny seeing the query come from a Cloudflare IP in Sydney and routing based on that. But the result was immediate. The CDN fetch dropped from 976ms to about 25ms.

This made us rethink our whole DNS setup. I checked the SG server too, same problem, same fix. We now make sure every bare metal server we provision gets `1.1.1.1` configured as the resolver before anything else, regardless of what the hosting provider defaults to.

## The result

```
# Before (OVH DNS in Europe)
976ms — Sydney → Canada → Germany → Canada → Sydney

# After (Cloudflare DNS, Sydney PoP)
~25ms — Sydney → Sydney
```

On a cache miss, that's the difference between a 1 second page load and sub-100ms. The random slowness on AU went away completely.

## What I learned

1. Check your DNS resolver location on every server you provision. When you run bare metal across multiple providers, each one sets their own default. Just set `1.1.1.1` or `8.8.8.8` and move on. Both have global presence.

2. In Rails, `Views: 1044ms` doesn't always mean your templates are slow. Rails counts all time spent rendering the response as "Views", including synchronous HTTP calls. A blocking `Net::HTTP` call inside a view helper looks like slow ERB in the logs.

3. A CDN cache HIT can still be slow. "HIT" means the edge had the content, but if the edge is on the wrong continent, you're still making a round-the-world trip for it.

4. Test CDN routing from your actual servers, not from your laptop. `curl -sI` with the `server` and `cdn-storageserver` headers tells you exactly where your traffic is going.

5. "It's slow sometimes" often points to a cache expiry pattern. If the intermittent latency lines up with cache TTLs, the problem is the fetch behind the cache, not the cache itself.