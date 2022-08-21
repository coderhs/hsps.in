---
title: "Open and View the Source Code of a Gem"
date: 2013-05-23T00:00:00-05:30
lastmod: 2022-08-20T22:22:45-07:00
draft: false
keywords: ["gem", "sublime", "vscode"]
description: ""
tags: ["gem", "sublime", "vscode"]
categories: ["ruby", "development"]
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
Now this is a small blog post, issued in  interest of  the beginners. On how to open and video the code of a ruby gem you are using.

<!--more-->

The code of  a ruby gem  you have installed, can be done using the bundle command.
```sh
bundle open <gem_name>
```
eg.
```sh
bundle open rails
```
Now this will open the rails gem directory in the editor you have specified for bundle. If you haven't specified any editor for it, then it would print the following error

To open a bundled gem, set `$EDITOR` or `$BUNDLE_EDITOR`
to assign an editor just use the export command
```sh
export EDITOR=<editor_name>
```
 eg:
```sh
export EDITOR=submlime-text-2
export EDITOR=vim
# 2022 Update
export EDITOR=code
```

