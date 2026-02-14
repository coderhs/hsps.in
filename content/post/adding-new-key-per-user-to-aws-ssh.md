---
title: "Adding New Key per User to AWS EC2 Instance"
date: 2022-07-01T22:58:41-07:00
lastmod: 2022-07-01T22:58:41-07:00
draft: false
keywords: ["SSH", "AWS", "EC2", "Linux", "Shell"]
description: "How to add SSH keys for multiple users on an AWS EC2 instance by generating key pairs and updating authorized_keys."
tags: ["SSH", "AWS", "EC2", "Linux", "Shell"]
categories: ["AWS", "Linux"]
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

When we create a new server in aws, it allows us to generate a key pair and attach it to the server. Now imagine you want to share access to this multiple people in your team, but you don't want to share your private key. This is what you need to do.

* Generate new key for each member of your team or ask each member for there public keys
* Add it to the authorized_keys list in your servers `.ssh` folder

<!--more-->

If you plan to generate key for each users, run the command `ssh-keygen`.

```sh
coderhs on Friday 23:31:36 🚀 > ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/home/coderhs/.ssh/id_rsa): newuser
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in newuser
Your public key has been saved in newuser.pub
The key fingerprint is:
SHA256:tBtoorlY0LUURkIs+kLV6nYTassdyB1Mhjgwinrw5XM coderhs@answer
The key's randomart image is:
+---[RSA 3072]----+
|.=+=O.. .o       |
|+.+=.= .+        |
|*o. + . .o       |
|oB + o..o..      |
|=.* + EoSo       |
|.o.+ = +. .      |
| .. . o...       |
|       ..        |
|       ..        |
+----[SHA256]-----+
```

You share the `newuser` file with your team member and then copy the contents of `newuser.pub`.

Then login to your server and go to the folder `cd ~/.ssh`, and open the file `authorized_keys` in vim and add the contents of newuser.pub to the end of the file.

eg:

```bash
ssh-rsa huge-random-string coderhs@answer
```

PS: Incase you generated the key, change coderhs@answer to something that will help you identify the user for whom you generated they key. `new-member@yourteam`.

When you want to diable this user from accessing the server again, delete that line from your `authorized_keys`
