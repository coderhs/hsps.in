---
title: "How I Setup Dark Theme in My Blog"
date: 2022-06-12T10:49:39-07:00
lastmod: 2022-06-12T10:49:39-07:00
draft: false
keywords: ["Website", "CSS", "Design"]
description: "Setting up dark theme in a hugo blog"
tags: ["css", "design"]
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

Dark mode or night mode is becoming more and more popular these days. As the amount of time we are spending in front of the computer increases, we started to look more into on how to improve our experience. Dark mode/Night mode is one such setting. What it does (mainly) is it makes all the white part of the application to black - major chunk of which would be the background. When the color of the background is made black, it emits less light. Now not all displays are the same, each display creates the dark color in different ways. Like:

* On an **`LCD screen`**. The liquid crystal elements block the white light from the backlight. And this results in a dark (almost black) pixel.
* On an **`OLED screen`**. The pixel is turned off, no light is emitted, this produces a deep black.
* On a **`CRT screen`**. The electron beam is turned off as it passes the pixel. Producing no phosphorescent glow.
* On an **`e-Ink screen`**. Little spherical beads which are half black/half white, are rotated so their black side is facing outwards.

Regardless as you can see, darker colors or more towards black the theme is the less lights are emitted and less strain to the eye. For me personally who spend more time in front of the computer than anything else having night color apps in your computer is god send. So when I myself have been a huge advocate of using night color/night mode in computer to improve developer health, but having a website so bright looked like an irony. Hence this article and why my blog has a dark mode now.

<!--more-->

My first step was to see if there was anything that could change my theme automatically. But more I researched more I found the automatic color changes to be not the perfect solution. So I decided to use a javascript library to get started, and then started to overwrite the particular settings I wanted one by one. It would also give me the option to further improve the UI in future. Hence the solution I ended up using was not 100% automatic and not 100% custom, but hybrid.

I used the library [Darkmode.Js](https://darkmodejs.learn.uno/). And then wrote a `dark.scss`, to overwrite few classes when the darkmode is activated.


```css
.darkmode--activated {
  .main {
    color: $light-gray;
  }

  .header {
    color: $light-gray;
  }

  .menu-item-link {
    color: $light-gray;
  }
  .post .post-content code, .post .post-content pre {
    background: $black;
  }

  .post-link {
    color: $theme-color;
  }

  .post .post-content .highlight>.chroma table::after {
    color: $white;
    background: $dark-gray;
  }

  .logo-wrapper {
    a {
      color: $theme-color;
    }
  }

  .footer {
    background-color: $black;
    padding-top: 2em;
    margin-top: 0em;
  }

  .footer .social-links .iconfont {
    color: $white;
  }

  .container {
    background-color: $black;
  }

  .pagination {
    a {
      color: $theme-color;
    }
  }
}
```

I like to write scss so that I can have variables in my css file. Using variables instead of hard coding the color code to black in each location, allows me to easily change the color in future, instead of having to search and replace black at all location.
