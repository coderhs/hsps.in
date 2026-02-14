---
title: "Append to File using Bash"
date: 2022-06-08T14:47:32-07:00
lastmod: 2022-06-08T14:47:32-07:00
draft: false
keywords: ["bash", "linux", "ubuntu", "til"]
description: "How to append text to a file in Bash using the >> operator, with examples and a warning about the > overwrite operator."
tags: ["bash", "linux", "ubuntu", "til"]
categories: ["bash", "ubuntu"]
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

<!--more-->

```sh
echo "Line you want to append" >> ~/ideas.md
```

The command here that ammends the line to the file is `>>`. If you place any string block before this operator and point it to a file, it will get appended.

```sh
tail -f something.log | grep important >> ./important.log
```

Note: if you use the operator `>` instead or accidentally, it will overwrite the whole file it is point to.

