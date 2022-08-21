---
title: "Splat Operator in Ruby"
date: 2013-09-25T00:00:00-05:30
lastmod: 2013-09-25T00:00:00-05:30
draft: false
keywords: ["argument", "code", "parameter", "ruby", "ruby 2.0", "splat"]
description: ""
tags: ["argument", "code", "parameter", "ruby", "ruby 2.0", "splat"]
categories: ["TIL", "Ruby"]
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

Ruby splat operator (*variable) is used to convert an array of elements into an argument list.

<!--more-->

Example Usecase

Date is passed by the user in the format : 2013/04/03   (YYYY/MM/DD), and you convert this date string to an array of integers using split and map.

```rb
"2013/04/03".split('/').map(&:to_i)
```
which would give the output `[2013,04,03]`.

Now to convert this into a Time object, we need to supply this array as arguments into the Time.new.

The format of which is
```rb
  new(year, month=nil, day=nil, hour=nil, min=nil, sec=nil, utc_offset=nil)
```
we can't put the above array as such to Time.new.We need to change the array to arguments such that the first element would be marked as year, second one the month, and  third one the day. Here is where we use, the splat operator.

```rb
Time.new *[2013,04,03]

# or

Time.new *"2013/04/03".split('/').map(&:to_i)
```

Ruby splat can be used to accept inputs to a method as an array
```rb
 def new_method foo, *bar
    puts foo.inspect
    puts bar.inspect
 end

 new_method 1, 2, 3, 4, 5, 6, 7
```
=>
```sh
     1
     [2, 3, 4, 5, 6, 7]
```

The rest of the inputs will get stored into the bar as an array. So, in cases where we don't know how many inputs we are expecting ,we can use this operator.

Convert Hash to array and array to Hash

Splat operator can be used to convert array to hash, and hash to array.
```rb
a = *{:a => 1, :b => 2}
```
=>
```sh
[[:a,1], [:b,2]]

```

```rb
Hash[*a.flatten]
```
 =>
```sh
 { :a => 1, :b => 2}
```



Double splat in ruby 2.0 +

I haven't explored the full possibilities of this operator. The double spat can be used to store the hash that is passed into a method.

```rb
def method **foo
  puts foo.inspect
end
method a: 1, b: 2, c: 3
```
=>
```sh
 {:a=>1, :b=>2, :c=>3}
```


Example with all of them together
```fb
def method a, *b, **c
   puts a.inspect
   puts b.inspect
   puts c.inspect
end

method 1, 3, 2, 3, 5, 12, 12, s: 1, b: 1
```
=>
```sh
1
[3, 2, 3, 5, 12, 12]
{:s=>1, :b=>1}
```
