---
title: "How I Use Tailscale to Host a Public App From My Laptop"
date: 2025-06-18T23:09:32+05:30
lastmod: 2025-06-18T23:09:32+05:30
draft: false
keywords: ["tailscale", "homelab", "devops", "docker", "hosting"]
description: "How I Use Tailscale to Host a Public App From My Laptop"
tags: ["tailscale", "homelab", "devops", "docker", "hosting"]
categories: ["homelab", "tailscale"]
author: "Harisankar P S"

# You can also close(false) or open(true) something for this content.
# P.S. comment can only be closed
comment: true
toc: true
autoCollapseToc: false
postMetaInFooter: false
hiddenFromHomePage: false
# You can also define another contentCopyright. e.g. contentCopyright: "This is another copyright."
contentCopyright: false
reward: false
mathjax: false
mathjaxEnableSingleDollar: false
mathjaxEnableAutoNumber: false

# You unlisted posts you might want not want the header or footer to show
hideHeaderAndFooter: false

# You can enable or disable out-of-date content warning for individual post.
# Comment this out to use the global config.
#enableOutdatedInfoWarning: false

flowchartDiagrams:
  enable: false
  options: ""

sequenceDiagrams:
  enable: false
  options: ""

---

I live in India, and getting a static IP at home isn’t straightforward. You usually have to go through customer care, sometimes upgrade to a more expensive plan, and even then it’s not always guaranteed. I wanted a simple setup to make one of my apps publicly accessible without paying a lot or dealing with all that.

So here’s what I did. I used **Tailscale**, **AWS Lightsail**, **Docker**, and **Nginx Proxy Manager** to expose my laptop to the internet, safely and securely. And right now, I'm running [https://easyclientlog.com](https://easyclientlog.com) - next.js application and [https://api.easyclientlog.com](https://api.easyclientlog.com) - ruby on rails application, using this exact setup.

## Getting a Static IP

I signed up on AWS and went to Lightsail. Picked Mumbai as the region and selected the cheapest Ubuntu server. AWS gives you a static IP for free (in lightsail) as long as it’s attached to a running instance, and the server itself is free for the first three months.

This server acts like a relay. It’s the public-facing machine. I don’t run the actual app on it, it just receives the request and forwards it to my laptop at home.

<!--more-->

## Installing Tailscale and NPM

On that Lightsail server, I installed Tailscale and Docker. Then I set up **Nginx Proxy Manager** (https://nginxproxymanager.com/) using Docker, which is what handles the actual proxying.

Ports 80 and 443 are open to the public. Port 80 redirects everything to 443 for HTTPS. Then Nginx the forwards traffic to the **Tailscale IP** of my spare home laptop.

One important thing to note is that the **admin panel of Nginx Proxy Manager runs on port 81**, and I’ve kept that accessible **only through the Tailscale network**. That means even if someone knows the IP and port, they can’t open it unless they’re on my Tailscale network.

## Running the App on My Laptop

On my laptop, I run both the frontend and backend in Docker containers. The frontend runs on port `4445` and the backend on `4446`.

Nginx on the server is configured to route:

- `https://easyclientlog.com` to my laptop’s Tailscale IP on port `4445`
- `https://api.easyclientlog.com` to port `4446`

From the outside, it looks no different fom hosting it on public server. But everything is running on my personal laptop at home.

## DNS and Domain Setup

I manage my domain through Cloudflare. It points to the static IP from Lightsail. Cloudflare also gives me basic protection, provides caching, handles SSL, and helps with fast DNS updates. So if I ever recreate the server, I just re-attach the same static IP and everything keeps working.

## Costs

There’s basically no cost involved apart from what I already pay for internet and electricity. The Lightsail server is free for three months. After that, I’ll probably just delete the server and spin up a new one and reattach the same static IP to avoid getting billed.

Being from India, power is another issue that I face. I choose a laptop as spare server for this purpose, and I got a small 5v ups for my ISP router as well.

## Why This Works For Me

This setup works well for early-stage projects or side hustles where I don’t want to commit to cloud hosting costs. My laptop has 16 GB RAM and an i5 11th gen processor, which is enough to handle a decent amount of traffic before things start getting serious. It also allows me to experiment with a better set of tools which requires more processing power then a free hosting cloud provides.

The whole thing gives me a public-facing app with minimal cost, full control, and no extra noise.

If you’re curious, you can check it out live at:

- [https://easyclientlog.com](https://easyclientlog.com) – the frontend
- [https://api.easyclientlog.com](https://api.easyclientlog.com) – the backend

Currently what you see at [https://easyclientlog.com](https://easyclientlog.com) is a static site. If you want to try out the app, feel free to register at [https://easyclientlog.com/register](https://easyclientlog.com/register). It’s still in active development. You can also sign up for the waiting list to get notified when the stable version is released.


