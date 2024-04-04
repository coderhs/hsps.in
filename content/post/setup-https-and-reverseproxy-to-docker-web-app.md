---
title: "Setup HTTP(s) and Reverse Proxy to Docker Web App"
date: 2023-08-03T22:31:40-07:00
lastmod: 2023-08-03T22:31:40-07:00
draft: false
keywords: ["nginx", "docker", "docker-compose", "setup", "proxy"]
description: "Setup HTTP(s) and Reverse Proxy to Docker Web App"
tags: ["nginx", "docker", "docker-compose", "setup", "proxy"]
categories: ["Docker", "devops"]
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

This article is about how to add https to a web app running in docker. For this we are using nginx to handle port 80 and port 443 and then reverse proxy the traffic to port 443 to the application port. In this article we are assuming you have credentials for the SSL certificate, another article will be written on how to do it with lets encrypt to create ssl certificates for free.

This article assume you know about ssl key files, docker and docker-compose. And for the sake of this article we are setting up `uptime-kuma`.

<!--more-->

To start you create a `nginx.conf` file:

```sh
server {
    listen 80;
    server_name yourdomain.com;
    return 301 https://$host$request_uri;
}

server {
    listen 443 ssl;
    server_name yourdomain.com;

    ssl_certificate /etc/ssl/ssl_keys/ssl_cert;
    ssl_certificate_key /etc/ssl/ssl_keys/ssl_key;

    location / {
        proxy_pass http://uptime-kuma:3001;  # Use the service name from docker-compose.yml
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        # the below settings are if you need websocket support as well
        proxy_http_version 1.1;
        proxy_set_header   Upgrade $http_upgrade;
        proxy_set_header   Connection "upgrade";
    }
}
```

After you create the nginx.conf and place it in the same folder as your docker-compose. Create a folder called ssl_keys and place your `ssl_certificate` and `ssl_private_key` in a folder called `ssl_keys`. Once that is done, add the following configuration to your `docker-compose.yml`

```yml
version: '3.3'

services:
  nginx:
    image: nginx:latest
    container_name: nginx-proxy
    ports:
      - 80:80
      - 443:443
    volumes:
      - ./nginx.conf:/etc/nginx/conf.d/default.conf:ro
      - ./ssl_keys/ssl_cert:/etc/ssl/ssl_keys/ssl_cert
      - ./ssl_keys/ssl_key:/etc/ssl/ssl_keys/ssl_key
    depends_on:
      - uptime-kuma
    networks:
      - reverse-proxy-network
  uptime-kuma:
    image: louislam/uptime-kuma:1
    container_name: uptime-kuma
    volumes:
      - ./uptime-kuma-data:/app/data
    ports:
      - 3001:3001
    restart: always
    networks:
      - reverse-proxy-network
networks:
  reverse-proxy-network:
    driver: bridge
```

What happening above is that we are putting both containers on a same network (virtual) called `reverse-proxy-network`. Nginx which is running on one container redirecting all the traffic to port 80 to port 443, thus enforcing https. And the traffic 443 is reverse proxies to port 3001. Uptime-kuma doesn't need to run SSL mode, nginx is now ensuring the traffic to the server is encrypted and then sending the decrypted traffic to `uptime-kuma` container.


