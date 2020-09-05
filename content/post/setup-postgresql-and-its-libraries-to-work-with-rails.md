---
title: "Setup Postgresql and Its Libraries to Work With Rails"
date: 2012-11-27
lastmod: 2020-09-05T12:38:45-07:00
draft: false
keywords: ['Tech', 'PostgreSQL']
description: ""
tags: []
categories: []
author: ""

# You can also close(false) or open(true) something for this content.
# P.S. comment can only be closed
comment: false
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


Recently lot of people have been asking me why they are not able to install the pg (PostgreSQL) gem even after installing PostgreSQL server in their system?

Well the answer is simple, the pg gem, requires the PostgreSQL development libraries to build native extensions to communicate with the PostgreSQL server. Native extensions refer to building ruby extensions or wrappers for existing C or C++ library.

One can install the development libraries of PostgreSQL by installing the libpg-dev package.
<!--more-->

The command below would install the last version of PostgreSQL available in the repository and its development libraries.

Ubuntu:
```sh
sudo apt-get install postgresql libpq-dev
```

CentOS / RedHat

yum install postgresql libpg-dev

```sh
yum install postgresql libpg-dev
```

After that trying gem install pg, should install everything smootly..

Happy Coding.. 🙂

UPDATE [2020-09-05]:

The developer libraries of postgresql are now packaged as `postgresql-server-dev-11`, 11 referring to the version.

Ubuntu:
```sh
sudo apt-get install postgresql-server-dev-11
```