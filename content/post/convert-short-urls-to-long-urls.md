---
title: "Convert Short Urls to Long Urls"
date: 2013-06-11T00:00:00-05:30
lastmod: 2013-06-11T00:00:00-05:30
draft: false
keywords: ["code", "Harisankar", "P", "S", "network", "programming", "ruby", "ruby 1.9.", "twitter", "url"]
description: ""
tags: ["code", "Harisankar", "P", "S", "network", "programming", "ruby", "ruby 1.9.", "twitter", "url"]
categories: ["Scirpt"]
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

How to get a long/actual url from a short url?

Sometimes when we try to fetch short urls, using curl, we get a blank page as reply or a 301 ERROR. The reason being that there is no html file stored at that location,only a header with details ,on where the person who has opened the url would be redirected to.

So to get the actual url or long url from the short url, we just have to read the location stored in the html header.

<!--more-->

```rb
require 'net/http'

uri = URI('http://bit.ly/ZPsGIF')
data = Net::HTTP.get_response(uri)
```
To get the location data, and for easy usage we convert this object to as hash.

`data.to_hash`, now running the command `data["location"]` will provide you with the long url.

Combining all the above codes the actual program would be :

```rb
require 'net/http'

uri = URI('http://bit.ly/ZPsGIF')
data = Net::HTTP.get_response(uri).to_hash
puts data["location"]
```
