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

```
root@Keval:/home/keval# docker run hello-world
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
0e03bdcc26d7: Pull complete 
Digest: sha256:8c5aeeb6a5f3ba4883347d3747a7249f491766ca1caa47e5da5dfcf6b9b717c0
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/
```

## Playing with Busybox

To get our hands dirty, let's run a [Busybox] container on our system to understand `docker run` command.

To get started, we first need to pull the busybox image from the Docker registry. 

```
root@Keval:/home/keval# docker pull busybox
Using default tag: latest
latest: Pulling from library/busybox
9758c28807f2: Pull complete 
Digest: sha256:a9286defaba7b3a519d585ba0e37d0b2cbee74ebfe590960b0b1d6a5e97d1e1d
Status: Downloaded newer image for busybox:latest
docker.io/library/busybox:latest
```

`docker pull` command fetches the required image and stores it to our system.

To view a list of all images on your system, use `docker images` command as follows:

```
root@Keval:/home/keval# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
busybox             latest              f0b02e9d092d        2 weeks ago         1.23MB
hello-world         latest              bf756fb1ae65        10 months ago       13.3kB
```

Now we can run a Docker container based on this image by executing `docker run` command in the terminal.

```
root@Keval:/home/keval# docker run busybox
root@Keval:/home/keval# 
```

However, we see no output. Why? Well, when we perform `docker run` command in terminal, the Docker Client finds the image (busybox in our case) loads up a container and then runs a commands in that container. While executing the `docker run` command above we did not give any command and thus, in this case the container ran an empty `run` command and exited.

We should try to echo something from the container.

```
root@Keval:/home/keval# docker run busybox echo "Busybox says hi"
Busybox says hi
root@Keval:/home/keval# 
```

Okay good, we see the echo message. Now to view a list of all the running/active containers we use `docker ps` command. However, we have no running containers and this command would result in a blank output. A more useful and powerful variant of this is the `docker ps -a` command which shows us a list of all the containers, be it in running  or exited state.

```
root@Keval:/home/keval# docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS                          PORTS               NAMES
907bef05f9bd        busybox             "echo 'Busybox says …"   26 seconds ago       Exited (0) 24 seconds ago                           wonderful_archimedes
1f6a478c7b38        busybox             "sh"                     About a minute ago   Exited (0) About a minute ago                       happy_bartik
d45fa34b3d93        hello-world         "/hello"                 26 minutes ago       Exited (0) 26 minutes ago                           sad_goldstine
```

It also possible for one to execute more than one command in a container. Running the `docker run` command with `-it` flag provides us with an interactive tty (TeleTYpewriter) which allows us to execute as many commands we want in a container.

```
root@Keval:/home/keval# docker run -it busybox sh
/ # ls
bin   dev   etc   home  proc  root  sys   tmp   usr   var
/ # uptime
 05:55:22 up  1:23,  0 users,  load average: 1.33, 0.81, 0.74
/ # exit
root@Keval:/home/keval# 
```

Now everytime we execute the `docker run` command, a new container is created which stays in the system storage if not removed. Thus, it is important to clean up the space by removing unwanted containers using `docker rm` command.

```
root@Keval:/home/keval# docker rm 78eac55cb3a3
78eac55cb3a3
root@Keval:/home/keval# 
```

Deleting containers by copypasting container IDs can be tedious at times and thus, you can delete a bunch of containers in single go by - 

```
root@Keval:/home/keval# docker rm $(docker ps -a -q -f status=exited)
907bef05f9bd
1f6a478c7b38
d45fa34b3d93
root@Keval:/home/keval# 
```

`-q` flag is used to return only numeric IDs and `-f` flag filters output based on the conditions provided (in our case containers with status equal to exited). 

Similarly, images can also be deleted using the `docker rmi` command.

```
root@Keval:/home/keval# docker rmi busybox
Untagged: busybox:latest
Untagged: busybox@sha256:a9286defaba7b3a519d585ba0e37d0b2cbee74ebfe590960b0b1d6a5e97d1e1d
Deleted: sha256:f0b02e9d092d905d0d87a8455a1ae3e9bb47b4aa3dc125125ca5cd10d6441c9f
Deleted: sha256:d2421964bad195c959ba147ad21626ccddc73a4f2638664ad1c07bd9df48a675
root@Keval:/home/keval# 
```

## Conclusion

There is more to Docker than just a bunch of basic commands covered above. However, I hope this blog got you interested in the Docker.

## References

1. [Docker for beginners]

[Mac]: https://docs.docker.com/docker-for-mac/install/
[Linux]: https://docs.docker.com/engine/install/ubuntu/
[Windows]: https://docs.docker.com/docker-for-windows/install/
[BusyBox]: https://en.wikipedia.org/wiki/BusyBox
[Docker for beginners]: https://docker-curriculum.com/