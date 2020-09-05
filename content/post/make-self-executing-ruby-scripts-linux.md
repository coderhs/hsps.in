---
title: "Make Self Executing Ruby Scripts Linux"
date: 2012-12-05
lastmod: 2012-12-05
draft: false
keywords: ['Ruby', 'Linux', 'bash', 'shell', 'unix']
description: "Make Self Executing Ruby Scripts Linux"
tags: ['Ruby', 'Linux', 'bash', 'shell', 'unix']
categories: ['Linux', 'Tech']
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


To run a ruby program we use the command `ruby`

`ruby program_name.rb`.

Ruby being an interpreter style language, each line is interpreted(read) one after the other and executable object code file is created for reuse like in case of compiler languages C, C++, etc.. To run a ruby code you need a ruby interpreter installed in your system.

<!--more-->

Linux (all UNIX like operating systems) has a way of executing a file that can help make a script self executable (that is making a ruby script ruby by just calling its name, with no need to specify the interpreter program). Its called #!. Dennis Ritchie, who introduced it, referred it as hash-bang though over the course of time it came be known by many names such as **shebang/hashbang/pound-bang/hash-exclam/hash-pling**.

This #! character are never used alone, it is followed by the location of the interpreter program using which you want to run that particular file (script).

Now to make our ruby script executable we need to add to the first line the following statement

```sh
#!/usr/bin/ruby
```

```puts "The ruby code, starts from line 2"``` How does this work?

A program is executed using a tool known as program loader. In linux when a program loader loads the file for execution and sees that there is #! notation in the first line, it would understand that this program needs to be passed to an interpreter program for execution. In case our ruby script when it gets executed ( $./program_name.rb) the program loader would pass it on to the ruby interpreter program that is defined in the hash-bang line as /usr/bin/ruby.

Thus the program loader would pass the file name and path to the ruby interpreter, in short the command $ruby program_name.rb ( $/usr/bin/ruby program_name.rb) would be execute.

Since the hash-bang line starts with hash ruby interpreter would consider it as a command line only.

The last and Final STEP ( IMPORTANT )

In Linux and any other UNIX like operating system no system can execute unless it is given the permission to execute. So we use the command chmod to give the permission to execute.

```sh
$chmod 755 program_name.rb
```

Following these steps you can make a ruby scrip executable.

Note :- The above mentioned method is not for ruby alone, any script can be made self executable using the above mentioned steps. We just have to change the program that is suppose to execute the program .

```sh
#!/bin/sh

#!/bin/csh

#!/usr/bin/perl -T

#!/usr/bin/php

#!/usr/bin/python -O

#!/usr/bin/ruby
```

Also the above mentioned is the location of the interpreter program if its in a different location then you will have to use that location or could use env – environment variable in linux thus the hash-bang statement changes to

```sh
#!/usr/bin/env ruby
```

That is the interpreter is searched among the environment variable, thus during run time the appropriate interpreter would be searched and used. For more details see the env wikipedia page

Happy Coding.