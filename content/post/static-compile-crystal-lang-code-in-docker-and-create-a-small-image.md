---
title: "Statically Compile Crystal Program and Distribute as docker Image"
date: 2023-04-02T20:22:46-07:00
lastmod: 2023-04-02T20:22:46-07:00
draft: false
keywords: ["docker", "crystal lang"]
description: "Statically Compile Crystal code in docker and create a small docker image for distribution."
tags: ["docker", "crystal lang"]
categories: ["crystal lang"]
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

In this article we are sharing how to statically compile a crystal program and then
share the executable inside a docker image. We will create the smallest docker image possible. Smaller images are easier to manage, distribute and boot.

Note: The way docker works, each command/line creates a layer (with context).

The good news about crystal lang is the they distribute the docker image with all the libraries so that we can build static compiled executable(s). Attaching the docker file I wrote to statically compile a crystal language program.

Note: The program uses the Kemal web framework, so this could also be considered an article on how to statically compile and distribute a kemal web app.

<!--more-->

```Dockerfile
FROM crystallang/crystal:1.0.0-alpine-build as base
RUN apk add sqlite-static yaml-static

FROM base as builder
RUN mkdir /pos
WORKDIR /pos
COPY shard.yml /pos/shard.yml
COPY shard.lock /pos/shard.lock
RUN shards install
COPY . .
RUN mkdir ./build
RUN cp -R ./assets ./build/assets
RUN cp -R ./views ./build/views
RUN crystal build --release --static -o app src/app.cr
RUN mv ./app ./build/app

FROM alpine:latest as release
COPY --from=builder /pos/build /pos
WORKDIR /pos
EXPOSE 9098
ENTRYPOINT ["/pos/app"]
```

We are using the crystal lang alpine build image. This is an image release/updated by the crystal team when they release a new version. Alpine linux is the smallest base image available for docker, we will be using this as our base image as well.

We are also using multi-stage docker build. We are compiling our code in `crystallang/crystal:1.0.0-alpine-build` then moving to `alpine:latest`. Hence our final image will be small, with nothing more than the required executables.


Now explaining each part one by one:

## Base Image

```Dockerfile
FROM crystallang/crystal:1.0.0-alpine-build as base
RUN apk add sqlite-static yaml-static
```

Our project uses yaml and sqlite, so we need to make sure there static libraries are present in our
image or we won't be able to compile.

## Caching the dependency installer

```Dockerfile
FROM base as builder
RUN mkdir /pos
WORKDIR /pos
COPY shard.yml /pos/shard.yml
COPY shard.lock /pos/shard.lock
RUN shards install
```

We are installing the shards in a prior step to ensure that docker caches it. After which we copy
the assets (html/css/js) required by the final build.

To create statically compiled executable we just need to pass the flag, and the compiler we statically link the required libraries.

## Static Compile the executable

```Dockerfile
RUN crystal build --release --static -o app src/app.cr
```

## Final Image

```Dockerfile
FROM alpine:latest as release
COPY --from=builder /pos/build /pos
WORKDIR /pos
EXPOSE 9098
ENTRYPOINT ["/pos/app"]
```

Finally we created the final image with a plain `alpine:latest` image, without any of the extras. Just copying the required executables and then exposing the port our app is running on.

In our case the final image was 78 MB.
