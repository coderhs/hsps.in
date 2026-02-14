---
title: "Review on Rail 4.3.0 Beta"
date: 2014-08-20T00:00:00-05:30
lastmod: 2014-08-20T00:00:00-05:30
draft: false
keywords: ["4.2.0.", "active", "job", "adequate", "record.", "global", "id", "rails"]
description: "Review of Rails 4.2.0 beta features including Active Job, Global ID, Adequate Record performance improvements, and web console."
tags: ["4.2.0.", "active", "job", "adequate", "record.", "global", "id", "rails"]
categories: ["Rails", "review"]
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
Active Job, deliver later will give us a lot of abstraction. One will have to move the business logic present in workers to models or services, but that would make our system more maintainable. If the Active Job is a simple plug and play then in future projects one can use delayed_job when the load is light and when the load increases they can shift to redis based resque or sidekiq. Thinking of migrating from one system to another always give the developers a head ache. This can essentially solve it. Active Job was planned to be a feature in rails 4.0 but later they decided not. I am glad that they are releasing it in 4.2

<!--more-->

I really like the [global id](https://github.com/rails/globalid) library, it really reduces the need to pass the object_id and then run Object.find(object_id). This will definitely makes the code much more clear. Update Note, 2019: It still doesn't support all the data type as argument, eg: Time with zone

Adequate Record, without any doubt, this is the one of the features we should look forward. Active record is a really awesome ORM, but at times the people often complain that this makes their rails application slow (most of the time its the developers fault not the library). The new version makes the entire system faster by almost 1/4th. You should have a look at the conference video (https://www.youtube.com/watch?v=NKs1PjkheQY&ab_channel=Confreaks) Aron Patterson explains everything about it in detail.

One thing i love about rails is that whatever the best practice is, they accept it and make it  the part of their work flow. A lot of developers use better_errors to make debugging easier and now a similar library would be added by default in the rails code. That console is based on IRB (the only negative :D). Personally i feel pry is better when it comes to debugging.
