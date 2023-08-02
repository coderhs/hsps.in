---
title: "Rails Copy Assets bundler Cache to Speed Up Build"
date: 2023-08-01T17:01:37-07:00
lastmod: 2023-08-01T17:01:37-07:00
draft: false
keywords: ["rails", "docker", "build", "system", "devops"]
description: "Rails Copy Assets bundler Cache to Speed Up Build"
tags: ["rails", "docker", "build", "system", "devops"]
categories: ["docker", "rails"]
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

The slowest step (in my experience and opinion) is compiling assets (js/css). Compiling assets takes time and slower when you do it for the first time. So to speed it up its recommended to copy the last compiled assets from the latest image you have of the repo.


```Dockerfile
COPY --from=docker-repo.link/image:latest /app/node_modules /app/node_modules
COPY --from=docker-repo.link/image:latest /app/public /app/public

RUN bundle exec rails assets:precompile
```

Now since the prior assets are in the new image, it won't compile the assets from scratch and the build will be faster.
