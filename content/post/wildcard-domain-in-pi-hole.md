---
title: "Wildcard Domain in Pi Hole"
date: 2022-09-04T11:36:43-07:00
lastmod: 2022-09-04T11:36:43-07:00
draft: false
keywords: ["homelab", "dns", "server", "linux", "dnsmasq"]
description: ""
tags: ["homelab", "dns", "server", "linux", "dnsmasq"]
categories: ["homelab"]
author: "Harisankar P S"

# You can also close(false) or open(true) something for this content.
# P.S. comment can only be closed
comment: true
toc: false
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

PiHole is a DNS server that will block ads and website on a DNS level. This is an integral part of my homelab as it blocks a lot of ads on my phones and system, and also easily allows us to manage domains for local application.

For homelab I have a docker-swarm which is managed using portainer. I have a nginx proxy manager to map the ports to a proper domain, even provide https using lets encrypt.But PiHole doesn't have a wildcard domain setting, hence after i set or before i set a domain my nginx proxy I need to add it to PiHole.

<!--more-->

To fix this we need to add a custom configuration in dnsmasq. Underneth PiHole its dnsmasq that manages the domain.

Login to server, and `cd /etc/dnsmasq.d/`.

On my current PiHole version (v5.11.4), you will see two files here

`01-pihole.conf`  `06-rfc6761.conf`.

To add custom wildcard top-level domain. You need to create a new file, lets call it: `07-hsps-home.conf` and add the following setttings:

```sh
address=/.hspshome/192.168.99.120
```

if you wanted to add a subdomain you could do it like so

```sh
address=/.machines.hspshome/192.168.99.120
```

Save it and restart the service or server, and you will be able to use the wildcard domain.
