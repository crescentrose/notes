---
description: 'Includes excerpts from my Docker talk on RubyZG, given on Oct 30th 2018.'
---

# Docker

Docker is a container-based virtualization engine. This means that, instead of spinning up a full blown virtual machine with its own layer of emulation, you host a _container_ which shares the kernel and the resources with your host OS's one, while not having direct access to the host device.

## What are containers?

Containers are running instances of Docker **images** that we build ourselves or pull from the internet \(Registry\). We use containers because we want our apps to be isolated and easily distributed. The isolation level is determined by our configuration - we can open up ports or share files or entire directories. The distribution is handled by a **registry.** Images can be based on other images - and those images are either pre-packaged apps or images of various operating systems. 

Basically, you can think of an image as a binary file on your computer, and a container as a running process. While you can make all sorts of changes to your system, and perhaps the program state itself, once you've closed the program you lose that state as long as you did not explicitly save it.

## How to get started

Set up Docker:

{% tabs %}
{% tab title="Linux" %}
This is for Debian based systems. If you are running another system, check out [the official docs](https://docs.docker.com/install/linux/docker-ce/binaries/).

```bash
# install dependencies
sudo apt update
sudo apt install apt-transport-https ca-certificates curl gnupg-agent software-properties-common

# add the Docker repos
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"

# install the Docker engine itself
sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io

# by default only root can access Docker containers
# if you want other users to be able to do that, add them to the
# `docker` group:
sudo adduser $USER docker
# note that this gives them the equivalent of full `sudo` access!
```
{% endtab %}

{% tab title="macOS" %}
1. Download the [installer](https://download.docker.com/mac/stable/Docker.dmg).
2. Follow the pretty installer.
3. Congratulations, you're done! You will need a Docker ID \(username and password\) to pull images from the Docker Hub, so you can [do that now](https://hub.docker.com/signup).

Remember that the macOS Docker engine starts up a virtual machine which, by default, has a fairly limited amount of memory and CPU. You might want to adjust that if you plan on doing intense work with Docker.
{% endtab %}

{% tab title="Windows" %}
Hahahahahahaha!
{% endtab %}
{% endtabs %}

## Basic commands

TODO: write cheat sheet

## Effective Dockerfiles

TODO: write about layering

## Compose

TODO: write about good Compose files

## Registry

### Self-hosting the Docker Registry



