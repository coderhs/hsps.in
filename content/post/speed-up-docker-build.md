---
title: "Speed Up Docker Build"
date: 2023-03-10T20:04:50-08:00
lastmod: 2023-03-10T20:04:50-08:00
draft: false
keywords: ["docker"]
description: "Breaking up steps to speed up docker build"
tags: ["docker"]
categories: ["docker"]
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

When we are packaging a big project, like for example a rails app. There are several steps that takes times to complete.

```sh
bundle install
yarn install
assets:precompile
```

etc..

Docker build and cache each step, and if the instruction or context hasn't changed then docker will use the cached version instead of rebuilding it.
So for us to make sure that Docker uses the cache, we need to make sure that docker can detect that there is no change in context/cache.


<!--more-->

To keep it simple:

Don't do:

```dockerfile
COPY . .
RUN bundle install
RUN yarn install
```

do

```dockerfile
COPY ./Gemfile .
COPY ./Gemfile.lock .
RUN bundle install

COPY ./package.json .
COPY ./yarn.lock .
RUN yarn install

COPY . .
```

Now docker would now use cache seeing that there is no change in the Gemfile, Gemfile.lock, etc. Before docker would have ran bundle and yarn because of any change in any file, will change the hash resulting in rebuilding the remaining steps.
