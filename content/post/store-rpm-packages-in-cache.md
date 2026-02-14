---
title: "Store RPM Packages in Cache"
date: 2012-01-29T00:00:00-05:30
lastmod: 2022-08-20T22:29:09-07:00
draft: false
keywords: ["cache", "centos", "downloadonly", "local", "mirror", "packages", "redhat", "repositories", "RHCE", "RPM", "tutorial", "yum", "yum", "install", "yum", "update"]
description: "How to cache downloaded RPM packages in CentOS and Red Hat by configuring yum keepcache or using the downloadonly plugin."
tags: ["cache", "centos", "downloadonly", "local", "mirror", "packages", "redhat", "repositories", "RHCE", "RPM", "tutorial", "yum", "yum", "install", "yum", "update"]
categories: ["red-hat", "rpm"]
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

Every time you download a RPM package for installation / updation via yum in Centos / Redhat system it gets deleted automatically after the procedure. Its a rather handy process by which your systems does not waste space storing the installation files.

Want to keep those packages in your system, and maybe create a local repository?

Well there are several ways. Simplest of them would be to edit the yum configuration file.

<!--more-->

```sh
vim /etc/yum.conf.file
```
On the 2nd line,of the file, there you will find the cachedir variable ( eg :- `cachedir=/root/packages/` ), one can set the location to which they want to store all these RPM packages.

but still you haven't enabled the option to keep cache. To do so you should edit the 3rd line which has the variable keepcache, by default keepcache should be set to 0 ( `keepcache= 0`, which means disabled) `change it to 1` to enable cache. This would enable storing of the RPM packages to the previously defined location.

For those who don't want to store every single packages they download in to the cache, but want to keep a couple of the packages can use yum download only plugin.

```sh
yum install yum-downloadonly
```

After installing now you can download rpm packages to a sepcific directory without installing or updating the system.

```sh
yum update mplayer -y --downloadonly --downloaddir=/root/packages
```

Now about how to create a local repository I will write in my next post? Stay tuned. and Have fun.
