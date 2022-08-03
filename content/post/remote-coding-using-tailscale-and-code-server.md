---
title: "Remote Coding Using Tailscale and Code Server"
date: 2022-07-03T13:52:46-07:00
lastmod: 2022-07-03T13:52:46-07:00
draft: false
keywords: ["Remote Development", "shell", "programming", "vs-code"]
description: "Coding in your dev machine from your tablet/phone remotely"
tags: []
categories: ["remote", "homelab", "dev", "vscode"]
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

This tutorial or concept uses two softwares to make remote coding on your home machine possible.

1. Tailscale
2. code-server

[Tailscale](https://tailscale.com) is basically a VPN using open source wireguard protocol. It creates an encrypted point to point connection so that only your devices can talk to each other.

So you can build a secure connection between your two computers over the internet. These two computers can be across the world, and they will be as if they are on a same network.

Tailscale provides a web interface and the setup makes it really simple, that anyone and everyone can easily get started.

Now since the two computers are on a network, you can run a web based or non web based service on a port and one machine can access it in the other machine.


The positives I like about tailscale:

1. No need for extra or special security/authentication. You just need to make sure they are members of this network.
2. You can easily disable and remove access to this network.
3. You can define two exit notes and make the machines' access internet using one of your machines. If you have a machine in India and one in US. You can easily make your whole network as if they are accessing web from India / USA. Really good with accessing the pesky geographic based access restricted website and mobile applications.
4. So easy to install and configure. (Learning curve is straight line)

[Code Server](https://github.com/coder/code-server) is basically a vscode as web service, that can be accessed over the web browser. VScode is an electron app written using typescript(javascript) so the experience is almost as same as the native/desktop application. The issue is that I haven't been able to make the settings sync work seamlessly yet. The plugins I could find used gist and github to share the json file, hence requires extra steps compared to just logging into github which is available in github and github code spaces.

Alternatives:

* VSCode-server: https://github.com/gitpod-io/openvscode-server
* Headscale: https://github.com/juanfont/headscale
