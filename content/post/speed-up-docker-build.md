---
title: "Speed Up Docker Build"
date: 2023-03-10T20:04:50-08:00
lastmod: 2023-03-19T20:04:50-08:00
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

Docker has become a popular tool in recent years for building and deploying applications in a portable and scalable way. However, the build process can sometimes be slow, especially for large applications with many dependencies. In this article, we'll explore how we can optimize our Dockerfile to speed it up.

When building a rails app for production we would need to do the following steps in the docker file

```sh
COPY . .
bundle install
yarn install
bundle exec rails assets:precompile
```

Docker after building each step it will cache each step, and if the instruction or context hasn't changed then docker will use the cached version instead of rebuilding it.
So we need to make docker understand that there is no change, hence we can use the cache instead of rebuilding it.

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
