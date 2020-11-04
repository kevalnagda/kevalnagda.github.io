---
layout: single
title:  "Getting started with Docker"
date:   2020-11-02 18:30:08 +0530
categories: jekyll update
---

![Docker](/assets/img/docker/docker-image.jpeg)

## What is Docker?

Docker is a software tool that helps you build, distribute, deploy and run applications inside an isolated sandbox called container.

## Why use Docker over Vitual Machine?

Docker uses containers for all of its operations and containers consume user space of an operating system. In simple terms, container is just a set of processes that are isolated from the rest of the system.

However, a Virtual Machine (VM) consumes user space as well as kernel space of an operating system for all of its operations. Each VM consists of an operating system and apps. Internally VMs use virtualized hardware, but share system hardware resources from the host operating system.

## Terminology

There is a bunch of Docker-specific jargon associated with the Docker ecosystem. So let's understand some important terms.

1. **Docker Image:** A blueprint of the application that helps us run containers.
2. **Docker Container:** Created from Docker image to run applications.
3. **Docker Daemon:** Background service running on host operating system that manages building, running and distribution of Docker Containers.
4. **Docker Client:** A command line tool that allows user to interact with the Docker Daemon.
5. **Docker Hub:** A pool of all available Docker Images where one can host their own Docker Images in the registries and pull other users' Docker Images.

## Installation

The _getting started_ guide on Docker website has detailed instructions for setting up Docker on [Mac], [Linux] and [Windows].

Installation can be verified by running the following command:

![docker run hello-world](/assets/img/docker/hello-world.png)

## Playing with Busybox

To get our hands dirty, let's run a [Busybox] container on our system to understand `docker run` command.

To get started, we first need to pull the busybox image from the Docker registry. 

![docker pull busybox](/assets/img/docker/busybox-image.png)

`docker pull` command fetches the required image and stores it to our system.

To view a list of all images on your system, use `docker images` command as follows:

![docker images](/assets/img/docker/docker-images.png)

Now we can run a Docker container based on this image by executing `docker run` command in the terminal.

![docker run busybox](/assets/img/docker/docker-run.png)

However, we see no output. Why? Well, when we perform `docker run` command in terminal, the Docker Client finds the image (busybox in our case)m loads up a container and then runs a commands in that container. While executing the `docker run` command above we did not give any command and thus, the container ran an empty command and exited.

We should try to echo something from the container.

![docker run busybox echo "Busybox says hi"](/assets/img/docker/docker-echo.png)

Okay good, we see the echo message. Now to view a list of all the running/active containers we use `docker ps` command. However, we have no running containers and this command would result in a blank output. A more useful and powerful variant of this is the `docker ps -a` command which shows us a list of all the containers, be it in running  or exited state.

![docker ps -a](/assets/img/docker/docker-ps.png)

It also possible for one to execute more than one command in a container. Running the `docker run` command with `-it` flag provides us with an interactive tty (TeleTYpewriter) which allows us to execute as many commands we want in a container.

![docker run -it busybox sh](/assets/img/docker/docker-it.png)

Now everytime we execute the `docker run` command, a new container is created which stays in the system storage if not removed. Thus, it is important to clean up the space by removing unwanted containers using `docker rm` command.

![docker rm](/assets/img/docker/docker-rm.png)

Deleting containers by copypasting container IDs can be tedious at times and thus, you can delete a bunch of containers in single go by - 

![docker rm $(docker ps -a -q -f status=exited)](/assets/img/docker/docker-rm-ps.png)

`-q` flag is used to return only numeric IDs and `-f` flag filters output based on the conditions provided (in our case containers with status equal to exited). 

Similarly, images can also be deleted using the `docker rmi` command.

![docker rmi](/assets/img/docker/docker-rmi.png)

## Conclusion

There is more to Docker than just a bunch of basic commands covered above. However, I hope this blog got you interested in the Docker.

## References

1. [Docker for beginners]

[Mac]: https://docs.docker.com/docker-for-mac/install/
[Linux]: https://docs.docker.com/engine/install/ubuntu/
[Windows]: https://docs.docker.com/docker-for-windows/install/
[BusyBox]: https://en.wikipedia.org/wiki/BusyBox
[Docker for beginners]: https://docker-curriculum.com/