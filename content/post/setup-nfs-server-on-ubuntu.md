---
title: "Setup NFS Server on Ubuntu"
date: 2022-09-05T18:29:12-07:00
lastmod: 2022-09-05T18:29:12-07:00
draft: false
keywords: ["nfs", "linux", "homelab", "storage", "persistance", "docker", "kubernetes"]
description: "Setup NFS Server on Ubuntu to have persistant storage for your docker swarm/kubernetes cluster"
tags: ["nfs", "linux", "homelab", "storage", "persistance", "docker", "kubernetes"]
categories: ["linux", "homelab"]
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

NFS - Network File System is a distributed file system protocol developed by Sun Systems. It facilitates a client computer to have access to file/folder over network like a local folder. This is a easy/simple way to provide persistant storage for your docker-swarm or kubernetes cluster. In both these cases, the container (or pod) run on one of the available nodes (servers), and when we have replicas it will be running on more than one node. So local storage on the server is out of the question.

With NFS we can directly mount the folder in the container. We can control everything from the configuration file. And thanks to the NFS storage we can increase and reduce node without worries as well.

<!--more-->

Once you have your ubuntu server ready follow the following steps:

## Step 1: Setup Software

```sh
sudo apt-get update
sudo apt-get install nfs-kernel-server
```

## Step 2: Make directory with right permissions

Once you have the server install, create folders that needs to be shared.

```sh
sudo mkdir -p /mnt/postgresql_data
sudo chown -R nobody:nogroup /mnt/postgresql_data
sudo chmod 777 /mnt/postgresql_data
```

## Step 3: Grant access to client machines

```sh
sudo vim /etc/exports
```

Add the following line to the file following the below structure:

```sh
<folder> <client_ip/network>(<permissions>)
```

eg:

```sh
/mnt/postgresql_data 192.168.99.0/24(rw,sync,no_subtree_check,no_root_squash)
```

The above line exposes the folder to all the clients in the network.

To explain the permissions part:

* rw: read/write (allowing the client to read and write)
* sync: requires changes to be written to the disk before applied
* no_subtree_check: eliminates subtree checking
* no_root_squash: Disable code preventing root user from writing to this folder

In most use cases the permissions (rw,sync,no_subtree_check) are used.

But since in this particular case we are mounting the NFS folder to docker container we need the no_root_squash. As containers boot into root/single user mode. So we will need to remove the check.


Once you added the file you need to export the nfs share directory.

```sh
sudo exportfs -a
```

You might have to restart the nfs service `sudo systemctl restart nfs-kernel-server`

## Step 4: Allow NFS access throught Firewall

Incase you have a firewall isntalled as well you will need to open NFS port.

If you are using ufw this is the foolowing command.

```sh
sudo ufw allow from 192.168.99.0/24 to any port nfs
```

Note: Default port for NFS is 2049

## Step 5: Add the volume to your docker-compose.yml

Eg `docker-compose.yml` file with NFS volume mounted.

```yaml
version: '3'
services:
  app:
    image: 'jc21/nginx-proxy-manager:latest'
    restart: unless-stopped
    ports:
      - '80:80'
      - '81:81'
      - '443:443'
    volumes:
      - nginxproxy:/data
      - nginxproxy-letsencrypt:/etc/letsencrypt
volumes:
  nginxproxy:
    driver: local
    driver_opts:
      type: "nfs4"
      o: "addr=192.168.99.114,rw"
      device: ":/media/nginx_proxy/data"
  nginxproxy-letsencrypt:
    driver: local
    driver_opts:
      type: "nfs4"
      o: "addr=192.168.99.114,rw"
      device: ":/media/nginx_proxy/letsencrypt"
```
