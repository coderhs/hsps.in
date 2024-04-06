---
title: "AWS ECR Lifecycle Policy"
date: 2024-04-06T06:21:43-07:00
lastmod: 2024-04-06T06:21:43-07:00
draft: false
keywords: ["space management", "aws cost management", "aws ecr", "aws", "ecr", "docker", "images", "repository", "cloud", "server admin", "server management"]
description: "How delete old and unused images in AWS ECR."
tags: ["aws", "ecr", "docker", "images", "repository", "cloud", "server admin", "server management"]
categories: ["AWS", "ECR"]
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

Amazon Elastic Container Registry (ECR) is a service provided by Amazon for hosting our Docker/container images. The cost of downloading these images to a server or ECS (Elastic Container Service) within the same zone is free. However, downloading or uploading from the public internet incurs costs, as does storage space usage.

During the development and deployment of your project, you may find yourself building images as frequently as every 30 minutes. This frequency reflects my own experience. Whenever new code is merged with the master branch, it triggers a code build pipeline. This pipeline ultimately pushes the latest image to ECR, typically tagged as "latest." Consequently, the previous image remains in the repository but becomes untagged. While retaining a few versions of older deployments facilitates easy rollbacks, after a day or two, they tend to become redundant. At this juncture, they serve no purpose other than incurring storage costs.

<!--more-->

To address this issue, ECR offers a setting called "lifecycle policy." This feature enables us to define how to manage tagged or untagged images, including those matching specific patterns. Accessing this setting involves selecting the repository and then choosing the corresponding action. The lifecycle policies option appears in the dropdown menu.

![Actions](/images/aws_ecr/aws_under_actions.png)

Selecting this option navigates to a page where you can manage your policies. If this is your first time setting up a policy, the page will be blank. In the example below, I already have one configured.

![Lifecycle Policies](/images/aws_ecr/lifecycle_policy_rules.png)

Here, you have the option to perform a dry run of your rule before finalizing it. Once a rule is implemented and images are deleted, there is no undo functionality available.

The rules are defined based on a priority list, where rules with higher priority execute first. The priority is indicated numerically, with lower numbers signifying higher priority (i.e., Rule 1 executes before Rule 2, and so forth).

![Rule Creation Page](/images/aws_ecr/rule_definition_page.png)

![Image Status](/images/aws_ecr/image_status.png)

The rule I implemented for my organization specifies the deletion of all untagged images when the total count exceeds five. However, if any untagged images are deemed important, they are tagged with an alternate name and retained.

In conclusion, effectively managing Docker/container images in Amazon Elastic Container Registry (ECR) requires strategic utilization of its features, particularly the lifecycle policy setting. By defining rules for handling tagged and untagged images, organizations can optimize storage usage and minimize costs. Regularly reviewing and refining these policies ensures that the repository remains efficient and clutter-free, facilitating smoother development and deployment processes. With careful planning and implementation, ECR serves as a reliable and cost-effective solution for hosting containerized applications within the Amazon Web Services (AWS) ecosystem.
