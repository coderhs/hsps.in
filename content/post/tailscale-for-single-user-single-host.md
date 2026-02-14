---
title: "Tailscale for Single User Single Host"
date: 2025-01-08T18:55:49+05:30
lastmod: 2025-01-08T18:55:49+05:30
draft: false
keywords: ["tailscale", "linux", "programming", "productivity", "single-host"]
description: "How tailscale can be useful for a person with a single computer"
tags: ["tailscale", "linux", "programming", "productivity", "single-host"]
categories: ["tailscale", "productivity"]
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
I often see articles discussing Tailscale in multi-device setups, but I wanted to share my experience of using Tailscale primarily with a single machine. To clarify, it's not technically a single device, but a combination of my work computer, phone, and a 10-inch Android tablet.

## The Problem: Inefficient Remote Access

I work from home, and my computer serves as both my personal and work machine. There have been many times when I’ve needed to assist colleagues or push changes while away from my desk. In emergencies, I used to rely on AnyDesk to remote into my computer via my phone or tablet. While AnyDesk and similar tools like TeamViewer provide visual control over the desktop, they often suffer from lag, disconnections, and poor responsiveness on weaker connections, making simple tasks like opening a terminal or editing a file feel like a chore. The lag becomes especially pronounced when navigating large projects or deploying code, turning a few-minute task into a frustrating, time-consuming ordeal.

<!--more-->

## Enter Tailscale: A Game Changer

Tailscale transformed the way I access my machine remotely. I installed Tailscale on my home computer, phone, and tablet. Since all of these devices are now connected through Tailscale’s private network, I can securely SSH into my home computer using [JuiceSSH](https://juicessh.com/) (an Android SSH client). With shell access, I can easily handle tasks such as:

- Deploying code
- Pushing unfinished work to a branch
- Pulling the latest updates

This seamless shell access alone was a massive upgrade for my workflow.

## Beyond SSH: Enhancing Remote Capabilities

Once I had reliable SSH access, I began exploring other ways to enhance my remote work capabilities using Tailscale. Here are two key use cases that have made a significant impact:

### 1. Remote Coding

In emergencies, I often need to review or write code. Thanks to Tailscale’s secure network, I can run web-based software directly from my home computer. My favorite tool for this purpose is [code-server](https://github.com/coder/code-server), which allows me to run a VS Code instance in my browser. Here’s how I set it up:

- Install and launch `code-server` on my home computer.
- Access it from my tablet through its Tailscale IP address.

Since this environment mirrors VS Code, I have full access to my workspace, including the terminal for compiling, building, and running commands. To enhance security, I make sure to bind the `code-server` instance to my Tailscale static IP.

There are many other web-based code editors you can use if VS Code isn’t your preference.

### 2. Accessing Files Remotely

Another frequent need is accessing files on my home machine. For this, I use [File Browser](https://filebrowser.org/), a lightweight web-based file manager. Here’s how I set it up:

```sh
filebrowser -a 100.64.17.1 -p 9090
```

This command makes the specified folder accessible through a web browser. I can download files directly to my phone or tablet, and in some cases, even preview files like text documents and videos directly in the browser. The ability to access files remotely has made my workflow much smoother.

## Expanding the Possibilities

With Tailscale, I gained direct terminal access to my home computer, transforming it into a true remote workspace that I can access from anywhere. When paired with my Samsung Tab S7 FE and a foldable wireless keyboard, I can:

- Continue coding sessions exactly where I left off.
- Access important files.
- Push critical updates or code changes seamlessly.

Additionally, if I’m using someone else’s computer temporarily, I can quickly install Tailscale, log in, complete my tasks, and then uninstall it when I’m done.

## Final Thoughts

Using Tailscale on a single machine (with supporting devices) has been a game-changer for me. The combination of SSH access, web-based coding, and file browsing has made remote work far more efficient and enjoyable. If you often find yourself needing remote access to a primary system, I highly recommend giving Tailscale a try.

Do share if you use tailscale like so or similarly, in the comment as well.

