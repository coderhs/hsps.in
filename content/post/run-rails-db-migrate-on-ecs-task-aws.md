---
title: "Run Rails DB Migrate on ECS Task AWS"
date: 2024-10-10T00:05:58-07:00
lastmod: 2024-10-10T00:05:58-07:00
draft: true
keywords: []
description: ""
tags: []
categories: []
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

AWS ECS (Elastic Container Service) is a amazon web service that is used to deploy docker containers on servers. It can be deployed to EC2 servers or standalone servers called fargate or your own self hosted servers. Since its deeply integratated with amazon environment it also make it easy and cheaper solution to mange containers in Amazon.

This article assumes you already have knowledge of ECS, and has a task definition setup for your rails application. It also assumes you know how to find the subnet name and security group name. We need that information as the container would be running
in such an environment.





<!--more-->
