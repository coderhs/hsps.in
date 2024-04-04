---
title: "Sidekiq Graceful Shutdown in Fargate ECS"
date: 2023-07-30T00:10:21-07:00
lastmod: 2023-07-30T00:10:21-07:00
draft: false
keywords: ["ecs", "fargate", "aws", "ruby", "sidekiq"]
description: "Sidekiq Graceful Shutdown in Fargate ECS"
tags: ["ecs", "fargate", "aws", "ruby", "sidekiq"]
categories: ["ECS", "Sidekiq", "Fargate"]
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

Sidekiq is the gold standard when it comes to background processing. I have been using since I started working with rails. Sidekiq took care of lot of the complexity of the background processing that allowed developers to concentrate on just there code/business logic.

One of the beautiful features of sidekiq was how it handled exit (SIGTERM and SIGKILL).

![Dont Sigkill](https://turnoff.us/image/en/dont-sigkill-2.png)
Source: https://turnoff.us/geek/dont-sigkill-2/

When sidekiq received the sigterm commands it stops accept new jobs, and just focus on finishing the job it is doing now and incase it can't finish, it will requeue the job to another available worker or keep it in the queue for another worker (or the same worker) to pick it up when available.
This was working so well for years on rails app I was working on, that I didn't even care about it. The mina command would trigger a sidekiq restart allowing sidekiq to gracefully exit and then respawn with the new code.

The above app was running on two linux servers everything manually configured, but recently to keep up with the growth of the app we moved to amazon ECS. The app was containerized to a docker image, and made to run on about 12 Fargate Instances with ability to auto scale to 36 based on CPU and RAM usage. We grouped instances for various purposes 2 for heavy jobs, 2 for faster (more memory jobs), 2 for file processing, 4 for web, 2 for API.

<!--more-->

Things were all good, the app was scaling well. Little or no slowness for our users, as the instances were scaling well during peak hours and scaling down during off peak hours. Things were all good and perfect in the promise land until we noticed that during deploy our long running jobs were not getting re queued, but dropped. The jobs never got finished, and the clients will have to trigger a re-run manually. As per the information I had collected, AWS ECS was sending a sig term command to the container giving sidekiq the time for a graceful shutdown (Our -t flag was set to 20 seconds). But looking through the logs of the container, and more importantly looking at sidekiq web we noticed that when the new sidekiq servers were setup the old sidekiq servers not shutting down but just dieing.

The usual flow of sidekiq lifecycle is - `running (green)` -> `quiet (red)` -> `stop (disappear)`.
But in our case we found that it was just disappearing from the page.

![Sidekiq Green](/images/sidekiq-gracefully-shutdown/green.png)

Using this peace of information we did more research into how ECS is giving this signal to the running application, and how its getting handled. This page gave us the whole explanation we were looking for: https://aws.amazon.com/blogs/containers/graceful-shutdowns-with-ecs/

Basically the way docker works, when it receives the signal it passes on to the first process of a docker instance. But if your initial process is actually just spawning the child/real process, then they would never get the SIGTERM.

Eg: if you are running sidekiq like or process like so:

```sh
sh -c "bundle exec -C config/sidekiq.yml -t 20"
```

sidekiq will never get the sigterm. Shell ignores SIGTERM by default.

You can fix this by making sure your command actually runs via `exec "$@"` and not sh.

Eg: (From the documentation)
```sh
./entry.sh /app app arguements
```

```sh
#!/bin/sh

## Redirecting Filehanders
ln -sf /proc/$$/fd/1 /log/stdout.log
ln -sf /proc/$$/fd/2 /log/stderr.log

## Initialization
# TODO: put your init steps here

## Start Process
# exec into process and take over PID
>/log/stdout.log 2>/log/stderr.log exec "$@"
```

Now this is what being done in most docker app. You add an `entrypoint.sh` like above and not `sh -c` like how we were doing.

The second option is to use a process manager like `tini` or `dumb-init`. These two makes it a bit more easier for us to manage thing as what it does is, it take the `SIGTERM` it receives and passes it to all of its children.

I decided to use `tini` as this is the one that amazon seems to be promoting and is already available in amazon docker environment.

So we changed our docker command to:

```sh
tini -s -- bundle exec sidekiq -C config/sidekiq.yml -t 20
```

The `-s` make tini take over PID 1, if tini started as any other PID. Docker gives the sig command to PID 1, so its important tini runs as pid 1.

After making the above change, when we send the ECS service force-deployment, it gracefully shutdown the sidekiq worker like before. It became quiet, try to finish the job and when it couldn't it rescheduled it.
