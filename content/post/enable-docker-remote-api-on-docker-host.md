---
title: "Enable Docker Remote Api on Docker Host"
date: 2022-09-06T20:21:42-07:00
lastmod: 2022-09-06T20:21:42-07:00
draft: false
keywords: ["docker", "http", "api", "remote", "linux", "ubuntu"]
description: "Enable Docker Remote Api on Docker Host"
tags: ["docker", "http", "api", "remote", "linux", "ubuntu"]
categories: ["docker"]
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

Docker engine has inbuild API service that allows you to control & modify your docker engine and docker instance using HTTP.

<!--more-->

To do that:
1. Navigate to `/lib/systemd/system` in your terminal and open `docker.service` file `sudo vim /lib/systemd/system/docker.service`
2. Find the line which starts with ExecStart and adds `-H=tcp://0.0.0.0:2375` to make it look like
`ExecStart=/usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock -H=tcp://0.0.0.0:2375`
3. `:wq` Save the modified file
4. Reload the docker daemon `sudo systemctl daemon-reload`
5. Restart the container `sudo service docker restart`
6. Test if it is working by using this command, if everything is fine below command should return a JSON `curl http://localhost:2375/services`
7. Ensure the port is open in your firewall `sudo ufw allow 2375/udp`
8. you also test it with `curl http://192.168.99.120:2375/services`

