---
title: "Reduce Cost of Aws Bill - Part 1"
date: 2023-03-19T19:52:42-08:00
lastmod: 2023-03-19T20:40:42-08:00
draft: false
keywords: ["AWS", "Cloud", "Amazon", "Bill"]
description: "List of things learned to reduce cost of AWS Bill"
tags: ["AWS", "Cloud", "Amazon", "Bill"]
categories: ["AWS", "Cloud", "Amazon", "Bill"]
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

Developing and deploying to the cloud has become a mandatory requirement than a nice to have skill. The promise of the cloud is that it abstracts all the complexity of managing a server and you only pay for what you use. If you accept that at face value, then the cloud is 100% legit. You only pay for what you use (just the cost is high), and they do abstract the regular complexity, and create some new complexity to worry about. There is a reason why AWS offers certificate programs. AWS Certified Cloud Practitioner/Architect/SysOps Admin/Developer/Solution Architect/Dev Ops Engineer all these shouldn't be there if all the complexity were truly abstracted.

Now that I have finished writing my frustration about cloud, lets return to the main topic of this article. Recently I was tasked with reducing our AWS bill. Our general approach to reduction in system performance have been to increase the server spec or DB spec. Looking at which resource was maxing out. DB or Server.

<!--more-->

The steps I did to reduce AWS Bill:

1. Delete old snapshots in EC2. We had disk snapshots as old as 2016 in our AWS account. We been paying for it. When we started out we manually installed and setup everything, thus made sense to create AMI to easily launch new instances. But items as old as 2016, made no sense. So deleting them saved some amount.

2. Deleting unused IP addresses, AWS doesn't charge if we are using IP Address but they charge us if we keep them unassigned.

3. Ensure blocks of resources are in the same region and same availability zone. All incoming traffic to AWS is free. Traffic between AWS regions are charged, Traffic between Different AWS Availability zone within a region is charged. AWS recommend multi zone deployment to ensure server availability even during geographic specific issues like - Earth Quake in the data center, storm, etc. Multiple region deployment is meant to reduce latency, and diverting the traffic/clients based on geography allows you to better manage your load. Having multi-zone deployment does allow you to better handle downtime or unexpected circumstances, but make sure you are smart about it. Like for example we had our web servers in us-west-2a and job servers in us-west-2b. This doesn't provide us with any benefit of fault tolerance. What we should do is we-west-2a servers should be sending jobs to job servers in us-west-2a and us-west-2b web servers sending jobs to us-west-2b job servers. If you are not worried about downtime like example if you ensure that the primary database has a standby server in another zone, and you have everything setup so that you can automatically or manually deploy your servers in another zone. Then feel free to keep all your servers in a single zone. In our case we saved around 2 TB of data transfer fee, (200 USD per month).

4. Use cloudfront 1 TB of data out is free and then its 0.085 per GB for the first 10 TB. From EC2 its free 100 GB out and then 0.09 per GB for the first 10 TB. Cloudfront provides us with better performance, and help us save some cost.

5. Upgrade the softwares we use. We upgraded Redis, PostgreSQL, web server, OS, etc. All this reduced our CPU and memory usage, for the same load. Resulting in US reducing our Server classes. The most save came from upgrading PostgreSQL from 11.x to 14.6. We are scheduled to upgrade it to 15.2 soon. Amazon only recently launched 15.2 in RDS.

6. Move few of our self hosted softwares to Lightsail. AWS lightsail is a VPS services that amazon provides that allow to launch servers with more predictable cost. We are using Lightsail to host:
      1.  ELK Cluster
      2.  Redis
      3.  Memcache (haven't we are considering)
      4.  Billing System
      5.  Scheduler

Thats it for Part 1.

As I optimize more, I will be adding more information. From this point on, I feel like we will be getting deeper into our marriage with Amazon. As we will explore and implement things that Amazon recommends such as: Reserved Instances, Spot Instance, or moving our app to use ECS with fargate/fargate spot.

This would allow us to handle load/peak traffic and come back to normal when there is less traffic/load.
