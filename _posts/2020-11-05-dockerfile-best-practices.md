---
layout: single
title: "Dockerfile best practices"
date: 2020-11-05 13:30:08 +0530
categories: jekyll update
---
 
Learning Docker and building Docker images from Dockerfile can be daunting at times, especially when you are a beginner. Following are a few important points to remember while dealing with Dockerfile and Docker images.
 
## Minimize the number of steps in the Dockerfile
 
Minimizing the number of steps in the Dockerfile not only helps you to improve the build but also significantly improves the pull performance.
Also, combining several steps into one line tends to create a single intermediary image instead of several i.e. each for one step.
 
## Start your Dockerfile with the steps that are least likely to change
 
This is the best advice one can get while learning to build a Docker image from Dockerfile. Usually, the best practice is to structure your Dockerfile as follows:
 
1. Install the tools needed to build your application.
2. Install all the required dependencies, libraries and packages.
3. Finally, build your application.
 
## A fairly straightforward approach to building Dockerfiles in an iterative manner would be as follows:
 
### 1. Pick the right base image
 
Picking the right image can be confusing at times. Thus, you should experiment with the one that best suits your requirements. For example to build a simple python application, one can select `python` as their base image instead of selecting a `ubuntu` base image and installing dependencies on top of it.
 
### 2. Go to the shell and build your environment
 
Building a docker image every time you make changes to your Dockerfile can be a hectic and time-consuming process. An alternative and efficient solution to this is to pull the preferred image locally and start a container in an interactive shell mode.
 
Once the steps execute in the container perfectly as needed, you can add those instructions to the Dockerfile immediately.
 
### 3. Add the steps to your Dockerfile and build your image
 
Stopping in middle, building and testing the docker image from the Dockerfile is also a crucial step. This step makes sure that you get the same desired results every time.
 
This newly built image can then be used to instantiate a new container with an interactive shell mode to proceed with installation and set-up steps.
 
### 4. Repeat steps 2 and 3
 
You might need to repeat steps 2 and 3 several times in order to thoroughly build a failproof Docker image and make sure that everything works fine as expected.
 
---
 
> [Dockerfile reference] is a good place to look for common Dockerfile syntax, warnings and documentation.
 
## References
 
1. [Dockerfile reference]
2. [Dockerfile tutorial by example]
 
[Dockerfile reference]: https://docs.docker.com/engine/reference/builder/
[Dockerfile tutorial by example]: https://takacsmark.com/dockerfile-tutorial-by-example-dockerfile-best-practices-2018/