---
layout: single
title: "Jenkins Docker tutorial"
date: 2020-11-10 12:00:00 +530
categories: jekyll update
---
 
In this blog, we learn how to setup Jenkins in a Docker container.
 
## What is Jenkins?
 
Jenkins is a self-contained, open-source automation server that can be used to automate all sorts of tasks related to building, testing, and delivering or deploying software.
 
Jenkins can be installed through native system packages, Docker, or even run standalone by any machine with a Java Runtime Environment (JRE) installed.
 
## Step 1: Pull Jenkins Docker image
 
We pull the [Jenkins Docker image] from the Docker Hub repository.
 
```
docker pull jenkins/jenkins
```
 
## Step 2: Run a Docker container
 
We can run a docker container based on the Docker image we just pulled.
 
```
docker run -p 8080:8080 -p 50000:50000 jenkins/jenkins:latest
```
 
where we expose port 8080 on the host and docker server to access Jenkins dashboard and port 50000 for the Jenkins API.
 
This command will create and run a Docker container for you, however, it won't save any data created when the container is exited or shutdown.
 
We can have persistent storage for Jenkins by executing the following command:
 
```
docker run -p 8080:8080 -p 50000:50000 -v /home/projects/Jenkins_Home:/var/jenkins_home jenkins/jenkins:latest
```
 
Here, `/home/projects/Jenkins_Home` can be replaced by the path where you wish to store your Jenkins data.
 
## Step 3: Setup Jenkins
 
Once Jenkins files have been extracted, the Jenkins server will be fully up and running at `http://localhost:8080`.
 
![](/assets/img/jenkins/1.png)
 
You can find the initial admin password at `/var/jenkins_home/secrets/initialAdminPassword` as mentioned on the login page.
 
Next, we can install plugins as per our requirement.
 
![](/assets/img/jenkins/2.png)
 
![](/assets/img/jenkins/3.png)
 
It may take some time depending upon the number of plugins you choose to install. Once the plugins are installed, you will be prompted to create a first admin user which you can skip if you wish to continue as an admin user.
 
![](/assets/img/jenkins/4.png)
 
Congratulations! You just set up the Jenkins server inside a Docker container.