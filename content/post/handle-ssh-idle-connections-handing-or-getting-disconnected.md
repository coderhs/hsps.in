---
title: "Handle SSH Idle Connections Freezing shell or Getting Disconnected"
date: 2024-04-04T03:13:24-07:00
lastmod: 2024-04-04T03:13:24-07:00
draft: false
keywords: ["linux", "shell", "ssh", "terminal", "remote"]
description: "How to prevent your ssh to a remote server does not idle, timeout or freeze."
tags: ["linux", "shell", "ssh", "terminal", "remote"]
categories: ["SSH"]
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

As someone who has been working with servers for over a decade and a half, I feel embarrassed to admit that I only
recently stumbled upon this setting. Throughout my years of connecting to remote servers over the internet, I've
encountered instances where connections freeze or break after a period of inactivity. Sometimes it happens more
frequently, while other times it never occurs at all. When I was in the US connecting to Amazon servers, there were
occasions when the connection remained stable for days on end. This led me to believe that the issue might be related
to the internet or the complexity of the network routing required to reach the server.

<!--more-->


However, I have finally discovered a solution—a setting that can be applied to your local machine: `ServerAliveInterval`.
This setting sends a ping to the server every x seconds to maintain the connection.
I set mine to 30 seconds, and since then, I have been able to keep my connections alive even during periods
of 2 days of inactivity 🎉.

To implement this configuration, you can edit your local/user SSH configuration file located at `~/.ssh/config`
 and add the following line:

```sh
ServerAliveInterval 30
```

Alternatively, you can apply this configuration globally to your system by editing /etc/ssh/ssh_config and
adding the same line at the end of the file.

Discovering this solution has been one of the most significant productivity boosts I've experienced recently,
 and I wanted to share it through this blog post.
