---
title: "Share Docker Images as Files"
date: 2023-03-26T21:27:35-07:00
lastmod: 2023-03-26T21:27:35-07:00
draft: false
keywords: ["docker", "files"]
description: "Share Docker Images as Files"
tags: ["docker", "linux", "basic admin", "administration"]
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

Docker is a popular tool to containerize and share images. Recently we ported a POS system into a docker image and had to share it with a client before we setup a private registry. This is how we share the image from my dev machine to clients local server.

Dev Machine: Kubuntu, 22.04

<!--more-->

```sh
docker save pos_web:latest | gzip > pos_web.tar.gz
```

You can export it to a regular tar file using the following command. Gzip just make the file smaller.

```sh
docker save -o pos_web.tar save pos_web:latest
```

You can then transfer the file to the client server. If you have ssh access to the server you can do so using a simple SCP command.

```sh
scp ./path/to/pos_web.tar.gz user@server:~/
```

The above command will transfer the file to the home folder.

Once the file is in the server you can load the image using the `docker load` command.

```sh
docker load ./pos_web.tar.gz
```

This command will load an image or repository from a tar archive (even if compressed with gzip, bzip2, or xz) from a file or STDIN. It restores both images and tags.
Source: https://docs.docker.com/engine/reference/commandline/load/

So no need to decompress before loading.


PS: Found this gem from the stackoverflow

Run all the above in a single command.

```sh
docker save <image> | bzip2 | ssh user@host docker load
```

or to see with progress bar

```sh
docker save <image> | bzip2 | pv | ssh user@host docker load
```

Source: https://stackoverflow.com/a/26226261/1749638
