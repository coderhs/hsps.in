---
title: "Test Scanner (scanner emulator) in SANE - Linux"
date: 2024-10-04T02:11:12-07:00
lastmod: 2024-10-04T02:11:12-07:00
draft: false
keywords: ["linux", "saned", "scanner"]
description: "How to enable the SANE test scanner emulator on Linux for development and testing without a physical scanner device."
tags: ["linux", "saned", "scanner"]
categories: ["linux"]
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

2024 [Hacktoberfest](https://hacktoberfest.com/) has begun, and I decided to dive into some open-source contributions! One project that caught my eye was the [Scanner Server](https://github.com/CoolCat467/Scanner-Server). It offers a web interface for using scanners, which seemed pretty interesting.

I was eager to contribute but quickly realized I didn’t have a scanner to test it with 😅. Fortunately, Linux’s SANE (Scanner Access Now Easy) software comes to the rescue! It includes a feature that lets you select a test scanner model. With this, you always have a virtual scanner called “test” available. While it only scans a black image, it behaves just like a real scanner for all practical purposes.

<!--more-->

Here’s how to enable the test scanner:

* Open the SANE configuration file:

```sh
sudo vim /etc/sane.d/dll.conf
```

* Search for the word `test` in Vim:

```vim
/test
```

This will take you to a line that looks like `#test`. Simply remove the `#` in front of `test` to enable it.

And that’s it! No need to restart any services—your virtual scanner is ready to go.
