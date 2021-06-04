---
title: "Install Ruby via Source Ubuntu"
date: 2012-11-15
lastmod: 2012-11-15
draft: false
keywords: []
description: ""
tags: []
categories: ['Tech', 'Ruby', 'Linux']
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

This blog post is for old style programmer who wish to install ruby via compiling the source code, like how I do it. This blog post is written in a just to know basis.

The source code of ruby can be downloaded from ruby’s official website http://www.ruby-lang.org/en/downloads/.

Dependency for Ruby and Ruby on Rails : ***libreadline, libyaml, libxml, libssl, zlib1g.***

In ubuntu you can have these libraries installed via the command line ( or if you are so old fashion you may google and find the source code for these packages as well)

<!--more-->

```sh
sudo apt-get install build-essentails zlib1g zlib1g-dev libreadline-dev libyaml-dev libssl-dev
```

After installing the dependency download the source code and extract it ( please not the installation will proceed if you miss some of the dependencies but missing then would make gem, bundle, irb to not function properly).

First extarct the code :

```sh
tar -zxvf [Name of ruby source code file].tar.gz
```

the configure the source code

```sh
./configure
```
the finally you make and install

```sh
make && sudo make install
```
To check if ruby is install you can use the command

```sh
ruby -v
```
or try out interactive ruby (irb)

```sh
irb
```
Now for those who are wondering how to install the latest version of rails, use the command:

```sh
sudo gem install rails
```
