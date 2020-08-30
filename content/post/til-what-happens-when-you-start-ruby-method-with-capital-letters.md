---
title: "[TIL] Starting Ruby Method With Capital Letter"
date: 2016-05-01T00:00:00+05:30
lastmod: 2020-08-30T11:46:25-07:00
draft: false
keywords: ['Ruby', 'Class', 'Objects', 'Basics']
description: ""
tags: ['Ruby']
categories: ['TIL']
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


Last day while I was browsing through some Java code online, I noticed that some method has names starting with capital letters. I Method names are written in small letters in ruby. This Java code made me wonder if there was anything preventing us from using method names starting with capital letter, in ruby. So I started my irb and wrote the following code.

```rb
def Test
  puts 'testing 123'
end
```
And when I ran to call the method Test in the irb i got the following error.

```rb
NameError: uninitialized constant Test
  from (irb):4
  from /Users/coderhs/.rvm/rubies/ruby-2.3.0/bin/irb:11:in `<main>'
```

But when I run the call as ```Test()``` in the irb i got the expected output testing 123.

So what I learned from this was that, when we start with capital it seems the interpreter is looking for classes, modules and constants called Test, and not looking for anything else. Thus giving you the error.

When I add the parenthesis to the name, we are doing a method call and hence the interpreter looks for a method called Test

PS: For those who might be wondering what would happen when we pass class name in all small letters

eg:

```rb
class testing
  def test
    puts 'testing'
  end
end
```
You will get an error:

```rb
class/module name must be CONSTANT
```

I am sure there is a much more clearer explanation for this, but this is just my inferences from my experiment.
