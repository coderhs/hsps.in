---
title: "Access History in IRB"
date: 2022-07-17T13:15:49-07:00
lastmod: 2022-07-17T13:15:49-07:00
draft: false
keywords: ["ruby", "rails", "irb", "til"]
description: "How to access and search your command history in Ruby IRB and Rails console using Readline::HISTORY."
tags: ["ruby", "rails", "irb", "til"]
categories: ["ruby", "rails", "irb", "til"]
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

Accessing the list of commands you have ran in your `irb` or `rails console`.

Running the following command in your console `Readline::HISTORY.to_a` returns the array of commands you have typed in console. After which you can treat it like any other array in ruby - search, sort, etc.
