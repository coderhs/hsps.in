---
title: "About Web Assembly. Example for Ruby Web Assembly"
date: 2023-05-19T21:12:50-07:00
lastmod: 2023-05-19T21:12:50-07:00
draft: false
keywords: ["ruby", "wasm", "web assembly"]
description: "Introduction to Ruby WASM. How to write ruby code in browser and how to get data to JS"
tags: ["ruby", "wasm", "web assembly"]
categories: ["web-assembly", "ruby"]
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

Any program to execute on a machine needs to be in machine code (1s and 0s). In some languages you compile the code directly to machine code, and some they converts it to intermediary code that runs on an virtual machine (which indirectly execute machine code). One of the biggest virtual machines in the world is a web browser. People write HTML,CSS and JS, and execute it by rendering the graphics and executing code on your machine. The browser have become so advanced that it takes care of security and safety so well. Thus being able to covert your program code or binary file into JS allows you to run your code on the browser and any machine that has a browser.

Now the idea of web assembly support in ruby (or any language) is not about writing website using ruby, though we can do that if we wanted to. The idea is to make a program or library that we have already existing in one language available for the web. If you can run ruby interpreter on your browser, then you can run any ruby code on your browser.

The things people doing with web assembly have been getting crazier. People are building more and more proof of concept ideas and apps in web assembly. The most exciting thing that impressed me recently was [webvm.io](https://webvm.io/). A virtual machine is running on your browser with linux. You can run linux commands, execute a python program, etc. This is a full fledged virtual machine (the one you run using docker), running on your browser. There are more projects that does that now:

* https://bellard.org/jslinux/vm.html?url=alpine-x86.cfg&mem=192
* https://copy.sh/v86/

You can host a web server or a rails app inside an image, which will then run on your browser serving the content. (Note: networking is still not native, so most use web sockets to emulate a network and make internet available inside the container).

The potential for this is huge, you can technically turn any machine that can run a web browser (Chrome or firefox) into a server.

<!--more-->

An example use case of web assembly has been a recent weekend project of mine: https://hsps.in/csvsqlweb/

In that project (proof of concept), the sqlite3 database is running on your browser using web assembly. No code is ever shared with a server, and its running entirely on your browser. Building more privacy friendly tools like this becomes possible. You can build a pdf converting website, and audio converter, etc to run on the browser with zero load on the server (other than serving static content) and the user need not worry about data leaving his computer.

Ruby 3.2 has added support for web assembly, which is exciting for me, as ruby is my primary language. I wrote a simple example app where ruby code is being executed on the browser, converting a column of data (copied from an excel sheet) to an array.

https://hsps.in/column_to_array/

```rb
# Ruby code that is running in the browser
require "js"
document = JS.global[:document]
button = document.getElementById "convert_text"
input_text  = document.getElementById "input"
result = document.getElementById "output"
button.addEventListener "click" do |e|
  luckiness = input_text[:value].to_s
  result[:innerText] = luckiness.split("\n").to_s
end
```

Links:

* https://github.com/ruby/ruby.wasm


