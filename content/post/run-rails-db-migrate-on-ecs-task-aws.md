---
title: "Running Rails Migrations in AWS ECS Using AWS CLI"
date: 2024-10-10T00:05:58-07:00
lastmod: 2024-10-10T00:05:58-07:00
draft: false
keywords: ["aws", "ecs", "rails", "db", "migrate"]
description: "Running Rails Migrations in AWS ECS Using AWS CLI "
tags: ["aws", "ecs", "rails", "db", "migrate"]
categories: ["aws", "rails"]
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

AWS ECS (Elastic Container Service) is a powerful Amazon Web Service that allows you to deploy Docker containers on various infrastructure options, including EC2 instances, Fargate (serverless containers), or even self-hosted servers. Being deeply integrated with AWS, ECS offers a cost-effective and seamless solution for managing containers in the Amazon ecosystem.

This guide assumes you are familiar with ECS and already have a task definition set up for your Rails application. Additionally, it assumes you know how to find your subnet ID and security group ID, as these will be required when running containers in your specific environment.

<!--more-->

We will also cover how you can use the same approach to run one-off tasks in ECS.

## Steps

1. **Install and Configure AWS CLI**
   Ensure the AWS CLI is installed on your local machine, and your credentials are configured.

2. **Prepare the Docker Image**
   Make sure the latest Docker image of your application is available in ECS.

3. **Collect Subnet ID and Security Group ID**
   Retrieve the necessary Subnet ID and Security Group ID from your AWS account. You can find this information from any of your currently running ECS tasks and copy it.

4. **Run the ECS Task**
   Use the following command to run your Rails migration task in ECS:

   ```sh
   aws ecs run-task  --cluster ecs-cluster-name
                     --task-definition ecs-task-definition
                     --launch-type FARGATE
                     --network-configuration "awsvpcConfiguration={subnets=[subnet-id],securityGroups=[sg-id],assignPublicIp=DISABLED}"
                     --overrides '{"containerOverrides":[{"name":"rails-container-name-in-task-definition","command":["bundle", "exec", "rails", "db:migrate"]}]}'
                     --count 1
   ```

   This command will trigger the creation of a new ECS task that runs your database migration. However, note that you won't see the output directly from the command.

5. **Check Task Status and Logs**
   To verify if the migration completed successfully, you can either log into the ECS console or retrieve the task ID from the command's JSON output, which is under the `taskArn` key.

   To fetch the logs from the task, use the following AWS CLI command:

   ```sh
   aws logs get-log-events        --log-group-name "/ecs/my-log-group"        --log-stream-name "ecs/my-task-id/my-container-name"
   ```

PS: If you want to run a similar one of tasks just replace the  `command` value in the step 4 instructions.
  eg: `["bundle", "exec", "rails", "db:create"]` or `["bundle", "exec", "rails", "db:seed"]`

## Automating Migrations During Deployments

Running this migration task manually for every deployment isn't recommended. In our setup, this command is the final step in our AWS CodeBuild pipeline. Once a new Docker image is pushed to AWS ECR and the ECS cluster restart is triggered, this command runs automatically to handle the migrations.
