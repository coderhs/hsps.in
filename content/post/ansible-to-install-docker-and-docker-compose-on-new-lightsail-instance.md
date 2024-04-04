---
title: "Using Ansible to Install Docker and Docker Compose on New Lightsail Instance"
date: 2023-08-06T21:59:35-07:00
lastmod: 2023-08-06T21:59:35-07:00
draft: false
keywords: ["ansible", "docker", "docker-compose", "playbook", "linux-admin", "ubuntu"]
description: "Using Ansible to Install Docker and Docker Compose on New Lightsail Instance"
tags: ["ansible", "docker", "docker-compose", "playbook", "linux-admin", "ubuntu"]
categories: ["ansible"]
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

Recently I have been working with deploying self hosted application in AWS lightsail for the company that I work for. My strategy for deploying application has been as follows:

* Launch a new ubuntu instance (since my team and I use it a dev machine we are familiar with it).
* Install Docker
* Install Docker Compose
* Run the self hosted app and its requires services using docker.
* Run nginx reverse proxy to serve the app over https

I am pretty much managing the whole app through files. Its just the initial setup that I am doing manually. I thought of creating a base linux image for the new servers, which would have docker and docker-compose pre installed. But lightsail didn't seem to have the option to use custom images, like EC2 servers.

Thus I decided to use ansible.

Ansible is an open source automation tool that is used for configuration management, deployment and task automation. It automates the steps that I do manually. It can run this command on multiple servers without having us do anything manually. We can also automate weekly maintenance check to login to all the servers, ensures all the security updates are installed, cache files are cleared, etc.

The thing I found most interesting about ansible compared to chef (another automation tool) is that it is agentless. That is it doesn't expect anything to be running on the new server other open ssh. Chef on the other hand requires an agent to be running on the server. There are its own advantages of having an agent running on the server, it can do updates much faster and keep everything in sync. But since that is not a requirement for me at the moment, I decide to use ansible. I will write another article on how to do this using chef.

<!--more-->

To get started using ansible, you need to install it in your machine following the instructions [here](https://docs.ansible.com/ansible/latest/installation_guide/installation_distros.html). I am using an ubuntu machine so the command I used was as follows:

```sh
sudo apt update
sudo apt install software-properties-common
sudo add-apt-repository --yes --update ppa:ansible/ansible
sudo apt install ansible
```

Once ansible is installed, create a new folder called `playbooks`. Inside of which create folder `setup-docker-and-docker-compose`. The steps of automation is called playbooks in ansible.

To get started create a new file inside the folder called `playbook.yml`. The file name doesn't have to be playbook.yml you can call it anything you want, but the play is written you `.yml` format.

This is my playbook to achieve what i want.

```yaml
- name: Install docker and docker compose
  hosts: all
  become: true
  remote_user: ubuntu
  vars:
    ansible_ssh_private_key_file: "~/.ssh/key.pem"

  tasks:
    - name: Update APT cache
      apt:
        update_cache: yes
      become: true

    - name: Install Docker dependencies
      apt:
        name:
          - apt-transport-https
          - ca-certificates
          - curl
          - software-properties-common
        state: present
      become: true

    - name: Add Docker GPG key
      apt_key:
        url: https://download.docker.com/linux/ubuntu/gpg
        state: present
      become: true

    - name: Add Docker APT repository
      apt_repository:
        repo: deb [arch=amd64] https://download.docker.com/linux/ubuntu {{ ansible_distribution_release }} stable
        state: present
      become: true

    - name: Install Docker
      apt:
        name: docker-ce
        state: present
      become: true

    - name: Ensure current user is in docker group
      user:
        name: ubuntu
        groups: docker
        append: yes
      become: true

    - name: Install Docker Compose
      shell: curl -fsSL https://github.com/docker/compose/releases/latest/download/docker-compose-Linux-x86_64 -o /usr/local/bin/docker-compose && chmod +x /usr/local/bin/docker-compose
      become: true
```

Let me explain the top level keys:

* Name - Name of the playbook
* hosts - The hosts where you want to run the play book, i specified all. The hosts are loaded from another file, which I will explain later.
* become - should we become another user (or become root)
* remote_user - login user name
* vars - environment variables
* tasks - the tasks that needs to run. The tasks definition here is a ansible based dsl, so there isn't any shell scripts being run here. But you can specify shell scripts if you want. Tasks like apt install, docker run, etc are already pre set and just needs to be call with the appropriate arguments.

There are more configurations available for ansible and you can learn them all from the documentation. https://docs.ansible.com/ansible/2.7/user_guide/become.html

The purpose of this article is just an introduction, and help people get started to automate server management using ansible.

Now once the playbook is written, you need to create a second file to specify the inventory. The file that has the list of servers. You can write it in many formats like .yaml, .ini, etc. Here I have used `.ini`.

My inventory.ini looks like below:

```ini
[new_server]
x.x.x.x
```

If I have more servers then i can add them to the list, and ansible will run the code on them all.

Note: An important thing to note is that, ansible expects your machine to be able to ssh into the new server by default. You can fix that by placing config in the `~/.ssh/config` or you can set the path to the `*.pem` file under the vars key like below:

```yaml
vars:
  ansible_ssh_private_key_file: "~/.ssh/key.pem"
```

Once you have all the configuration setup you can run the playbook as follows:

```sh
ansible-playbook -i inventory.ini playbook.yml
```

You can change the files that you want to run here. I plan to keep an `.ini` file with all the servers and a `.ini` file where i will just place the ip of the new server.

Note: This can and should be possible with a single file, I will do that as I learn more about it.

Another thing to note that ansible is written to be idempotent, such that no matter how many times you run the result is the same. So some of the tasks like `apt install`, it doesn't install gain if its already installed. For code that can't be checked if its already ran before it will run again.

This has saved me the effort to login and setup everything myself, and easily control new servers using files as well.

