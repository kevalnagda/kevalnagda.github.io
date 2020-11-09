---
layout: single
title: "Dockerfile tutorial"
date: 2020-11-04 13:30:08 +0530
categories: jekyll update
---

Dockerfile is a text file that defines a set of commands or operations which aid you to build your own custom Docker image.

## Why would you want to use a Dockerfile?

Well, there are times when existing docker images don't satisfy your project needs and you want to do things differently. Docker helps you achieve this in an easy way, by just writing a Dockerfile!

## Let's dive in

We are aware that the available alpine image does not contain git, curl and vim installed by default. Thus, for learning purposes we create a new custom image based on alpine which contains git, curl and vim.

### 1. Create a Dockerfile

First of all, create an empty directory and an empty file inside that directory with the file name `Dockerfile`.

```
touch Dockerfile
```

### 2. Define a base image

Every Dockerfile must start with a `FROM` command. You can create an image from scratch, however there are a bunch of base images available which you can directly use by using the `FROM` command.

```
FROM alpine:3.4
```

### 3. Add instructions to install packages

Here we use the `RUN` instruction in Dockerfile to execute commands and install required packages.

```
RUN apk update
RUN apk add git
RUN apk add vim
RUN apk add curl
```

**NOTE:** It is ideal to execute multiple commands in a single `RUN` command as each instruction in Dockerfile creates an intermediary container.

An efficient approach to the example would be as follows:

```
RUN apk update && \
    apk add git && \
    apk add vim && \
    apk add curl
```

### 4. Building the image

You can build an image with the help of `docker build` command which automatically builds an image with nametag provided after `-t` flag as `<user_name>/<image_name>`.

```
$ docker build -t kevalnagda/apline-smart:1.0 .
Sending build context to Docker daemon  2.048kB
Step 1/5 : FROM alpine:3.4
3.4: Pulling from library/alpine
c1e54eec4b57: Pull complete 
Digest: sha256:b733d4a32c4da6a00a84df2ca32791bb03df95400243648d8c539e7b4cce329c
Status: Downloaded newer image for alpine:3.4
 ---> b7c5ffe56db7
Step 2/5 : RUN apk update
 ---> Running in 42d39c87ae89
fetch http://dl-cdn.alpinelinux.org/alpine/v3.4/main/x86_64/APKINDEX.tar.gz
fetch http://dl-cdn.alpinelinux.org/alpine/v3.4/community/x86_64/APKINDEX.tar.gz
v3.4.6-316-g63ea6d0 [http://dl-cdn.alpinelinux.org/alpine/v3.4/main]
v3.4.6-160-g14ad2a3 [http://dl-cdn.alpinelinux.org/alpine/v3.4/community]
OK: 5973 distinct packages available
Removing intermediate container 42d39c87ae89
 ---> e5872a7d6fe7
Step 3/5 : RUN apk add git
 ---> Running in 335a8120b6b8
(1/6) Installing ca-certificates (20161130-r0)
(2/6) Installing libssh2 (1.7.0-r0)
(3/6) Installing libcurl (7.60.0-r1)
(4/6) Installing expat (2.2.0-r1)
(5/6) Installing pcre (8.38-r1)
(6/6) Installing git (2.8.6-r0)
Executing busybox-1.24.2-r14.trigger
Executing ca-certificates-20161130-r0.trigger
OK: 22 MiB in 17 packages
Removing intermediate container 335a8120b6b8
 ---> ef8d1eda0212
Step 4/5 : RUN apk add vim
 ---> Running in 48948ba27f5e
(1/5) Installing lua5.2-libs (5.2.4-r2)
(2/5) Installing ncurses-terminfo-base (6.0_p20171125-r0)
(3/5) Installing ncurses-terminfo (6.0_p20171125-r0)
(4/5) Installing ncurses-libs (6.0_p20171125-r0)
(5/5) Installing vim (7.4.1831-r3)
Executing busybox-1.24.2-r14.trigger
OK: 54 MiB in 22 packages
Removing intermediate container 48948ba27f5e
 ---> 614ca6706e18
Step 5/5 : RUN apk add curl
 ---> Running in cccca59dceac
(1/1) Installing curl (7.60.0-r1)
Executing busybox-1.24.2-r14.trigger
OK: 54 MiB in 23 packages
Removing intermediate container cccca59dceac
 ---> 7f6efe76a85e
Successfully built 7f6efe76a85e
Successfully tagged kevalnagda/apline-smart:1.0
```

We can check the new image by executing the `docker images` command as follows:

```
$ docker images
REPOSITORY                TAG                 IMAGE ID            CREATED             SIZE
kevalnagda/apline-smart   1.0                 7f6efe76a85e        4 minutes ago       48.7MB
alpine                    3.4                 b7c5ffe56db7        21 months ago       4.82MB
```

If you look at the output of the `docker build` command, you would notice that there are 5 steps, namely Step 1/5, Step 2/5, Step 3/5 and so on...

The reason for this is that docker builds images by executing the instructions mentioned in the Dockerfile one at a time. Another thing to note here is that with every step in the build process, Docker will create an intermediary image for that particular step. This is called image layering and its main advantage lies in image caching.

If you were to rebuild the image, you would notice that the build was faster as compared to the build before that since the build was done from the cache. This behaviour makes our life a lot easier.

## Dockerfile instructions

### 1. `FROM`:
Let's set a base image in the Dockerfile. The instruction is in the form of `FROM <base_image>[:tag]`.

### 2. `RUN`:
Let's you run commands and it's one of the most used instructions in Dockerfile. 

`RUN` instruction has two forms, where `RUN <command>` is called the shell form and `RUN ["executable", "param1", "param2"]` is called the exec form.

### 3. `COPY`:
Helps you copy files and directories to your Docker image. The instruction is in the form of `COPY <src> <dest>`.

### 4. `ADD`:
Helps you add files and directories to your Docker image. The instruction is in the form of `ADD <src> <dest>`.

**NOTE:** The main difference between `ADD` and `COPY` command is that the former one is more advanced and has additional features like pulling files from url sources, recognizing and handling a lot more file formats than the later one.

### 5. `ENV`:
Let's you define environment variables in Dockerfile.

### 6. `WORKDIR`:
A way to define the working directory for your project.

### 7. `EXPOSE`:
It informs you about the exposed ports your application is listening on.

### 8. `CMD`:
It lets you specify which component is to be run by your image on execution of the container. The format is given as `CMD ["executable", "param1", "param2", ...]`.

One important thing to note here is that there should only be **one `CMD` instruction** in a Dockerfile. If more than one `CMD` instruction is present then only the last one will be used during execution.

### 9. `ENTRYPOINT`:
When the main executable is used in this instruction then the parameters provided in `CMD` instruction will be added as parameters to the `ENTRYPOINT` instruction. Example:

```
ENTRYPOINT ["git"]
CMD ["--help"]
```

## References

[Dockerfile tutorial by example]

[Dockerfile tutorial by example]: https://takacsmark.com/dockerfile-tutorial-by-example-dockerfile-best-practices-2018/