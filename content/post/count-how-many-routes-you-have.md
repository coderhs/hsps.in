---
title: "Count How Many Routes You Have"
date: 2022-05-17T17:21:51-07:00
lastmod: 2022-05-17T17:21:51-07:00
draft: false
keywords: []
description: ""
tags: ['Rails']
categories: ['TIL']
author: ""

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

It start with one, then two and it keeps on growing. The number of routes in your ruby on rails project can be a reflection of how complex your project is becoming. And if you are curious like me to know how many routes your project has, just run the following command in your rails console.

```rb
::Rails.application.routes.routes.size
```

To run without starting your rails console:

```rb
rails r 'puts ::Rails.application.routes.routes.size'
```
