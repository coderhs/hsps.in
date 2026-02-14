---
title: "Amazing Emoji Keyboard in Linux"
date: 2022-08-01T18:59:18-07:00
lastmod: 2022-08-01T18:59:18-07:00
draft: false
keywords: ["emoji", "keyboard", "KDE", "linux", "kubuntu", "splatmoji", "xdotool", "xsel", "rofi", "shortcut", "emoji keyboard"]
description: "Setup an emoji keyboard linux macOS"
tags: ["emoji", "keyboard", "KDE", "linux", "kubuntu", "splatmoji", "xdotool", "xsel", "rofi", "shortcut", "emoji keyboard"]
categories: []
author: "Harisankar P S"

# You can also close(false) or open(true) something for this content.
# P.S. comment can only be closed
comment: true
toc: true
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

I am a developer who moved from Windows, to Linux, to macOS, to Linux. A typical developer's journey 😆. So moving from
windows to Linux was over 13-14 years ago. I didn't do much from the windows world other than games and single executable
files. But moving from macOS to Linux, has been a bit tough. KDE has been able to provide me with everything I want and
more. The one thing that I missed the most was the emoji keyboard, with easy to launch shortcut.

My usage of emoji was at its peak during macOS days, I used emoji for my communications, commits, function name and
even terminal aliases. 😈 Not everyone was a fan of my emoji usages, but I loved it.

<!--more-->

After moving back to linux, I missed it quite a bit and always have been on the lookout for a good substitute.
Today, I finally found it.

https://github.com/cspeterson/splatmoji

![splatmoji](/images/splatmoji_kde_shortcut/emoji_keyboard.png)

Now, KDE(Kubuntu) has an emoji selector/keyboard, but the flow is not the same as in macOS.

The workflow macOS has was:
1. You press ctrl+shift+space
2. The emoji keyboard opens
3. You select the emoji you want
4. Its pasted on the cursor location - be it terminal/document/anywhere that accepts text

## Installation

I recommend installing it as a debian package and then customizing it.


```sh
sudo apt-get install xdotool rofi xsel
wget https://github.com/cspeterson/splatmoji/releases/download/v1.2.0/splatmoji_1.2.0_all.deb
sudo dpkg -i splatmoji_1.2.0_all.deb
```

Test it:

```sh
splatmoji copy
```

Now, once tested, we need to do some customization followed by setting up keyboard shortcut to launch it for easy access.

## Customization

create the following file: .config/splatmoji/splatmoji.config

```sh
# history_file=~/.local/state/splatmoji/history
history_length=5
paste_command=xdotool key ctrl+shift+v
rofi_command=rofi -dmenu -p '' -i -monitor -2
xdotool_command=xdotool sleep 0.2 type --delay 100
xsel_command=xsel -b -i
```

I need this because ctrl+shift+v is paste for me and not the default ctrl+v

## Setting up KDE Shortcut

1. `System Shortcuts > Custom Shortcuts`

![System Shortcuts > Custom Shortcuts](/images/splatmoji_kde_shortcut/new_location.png)

2. `Edit > New > Global Shortcut > Command/URL`
3. Give it a name or Emoji (or anything you like)
4. Trigger (Trigger tab): set the short cut you want. I used `ctrl+alt+space`.

![Trigger tab](/images/splatmoji_kde_shortcut/trigger_tab.png)

5. Action  (Action tab): `splatmoji copypaste`

![Action tab](/images/splatmoji_kde_shortcut/action_tab.png)

## More Emoji

Even though there are a lot of emojis in the repo, my usage requires more 😁.
To feed one's greed, they can create the following folder.

```sh
/home/coderhs/.local/share/splatmoji/data
```
Then place emojis inside a tsv file as follows. `<Emoji source name>/<any_name>.tsv`

A simple collection to add:

```sh
mkdir rofi-emoji
cd rofi-emoki
curl -fsSL https://raw.githubusercontent.com/Mange/rofi-emoji/master/all_emojis.txt > emoji.tsv
```

## Final words

The only concern right now is I have too much steps, to make it the way I want. So I need to write a script to
automate all the above steps. I need to find how to setup the keyboard shortcuts using command line, and I will
publish the script.
