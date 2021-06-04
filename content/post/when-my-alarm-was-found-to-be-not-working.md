---
title: "When My Alarm Was Found to Be Not Working"
date: 2013-03-15
lastmod: 2020-09-18
draft: false
keywords: []
description: "When My Alarm Was Found to Be Not Working"
tags: ['ruby']
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

Due to some unfortunate events

1. Loosing my phone 
2. K-alarm crashing

I faced a common problem for many people "No alarm to wake me up in the morning". Like every single man, I love sleeping. So unless an alarm or person wakes me up, I will continue to enjoy sleeping - until the urge from my second passion (hunger) wakes me up.

Despite the fact that I love sleeping, my team mates at Ruby Kitchen, won't be happy with me coming late to work. Challenged with such a problem, I did what every computer hacker would do.

**Wrote a program to wake me up, at 6:00 AM 😁**

<!--more-->

Wrote a handy little Ruby program (Object Oriented, as I see the world as Objects), to wake me up at 6 AM.
To play the music I used the system command of Ruby to run the vlc player with maximum volume (--volume [0..512]).

```rb
class WakeMeUp
  def initialize(alarm_time,music = "/mount/data2/Songs/11.Mazhathullikal.mp3")
    @alarm_time = alarm_time
    @music = music
    @run = true
  end

  def its_time
     system("vlc #{@music} --volume 512")
  end

  def check_time
     while @run
        if Time.now >= @alarm_time
           its_time
          @run = false
        else
           sleep(@alarm_time-Time.now)
        end
    end
  end
end

@wake_me_up = WakeMeUp.new Time.new 2013,03,15,6
@wake_me_up.check_time
```



More that can be done

* make it accept command line arguments
* Maybe use libmplayer or ffmpeg so that there is no dependency on actual application

PS:- Just sharing to help those people who might find themselves in a similar problem as mine