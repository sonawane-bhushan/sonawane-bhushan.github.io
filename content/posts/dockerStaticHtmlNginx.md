---
title: "Static HTML with Nginx Image"
date: 2023-04-15T11:30:03+00:00
weight: 1
aliases: ["/docker-nginx"]
tags: ["docker", "nginx", "beginners"]
author: "Bhushan Sonawane"
showToc: true
TocOpen: false
draft: false
hidemeta: false
comments: true
description: "How to create a Docker Image for static HTML content using nginx"
canonicalURL: "https://canonical.url/to/page"
disableHLJS: true # to disable highlightjs
disableShare: false
disableHLJS: false
hideSummary: false
searchHidden: true
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowWordCount: true
ShowRssButtonInSectionTermList: true
UseHugoToc: true
cover:
    image: "<image path/url>" # image path/url
    alt: "<alt text>" # alt text
    caption: "<text>" # display caption under cover
    relative: false # when using page bundles set this to true
    hidden: true # only hide on current single page
---

In this blog we will take a look at how to create and deploy static html with nginx as base image 

This blog is a beginners guide on how to create a basic docker image using nginx as base image which will help understand some of the basic concepts of docker. 

Before diving into the topic let's understand some basics about docker and nginx

## Docker
From Docker Documentation - Docker is an open platform for developing, shipping, and running applications. Docker enables you to separate your applications from your infrastructure so you can deliver software quickly. With Docker, you can manage your infrastructure in the same ways you manage your applications. By taking advantage of Docker’s methodologies for shipping, testing, and deploying code quickly, you can significantly reduce the delay between writing code and running it in production.

Simply speaking imagine you have a toy that needs lots of pieces to work, like a puzzle. You have to put all the pieces together in the right way for the toy to work. Docker is like a special box that you can put the toy in, along with all the pieces it needs to work. Then, you can take the box to different places and the toy will still work, because everything it needs is in the box. This is like putting an application in a container, which has everything it needs to work, so you can run it on different computers without worrying about whether they have everything it needs.

### What is docker image 
A Docker image is a standalone, executable package that contains everything needed to run an application, including the code, dependencies, and system libraries. It is created from a Dockerfile, which is a script that contains instructions for building the image. They are designed to be portable and can be easily distributed and deployed to different environments. 
Docker images are stored in registry such as Docker hub and can be pulled to create docker containers, which are instances of the image. 

### What is Dockerfile
Dockerfile is like a receipe to create the docker image. It contains series of instructions that tell docker how to create an image, such as what base image to use, which command to run, which files to include, how to setup environment. By using Dockerfile to create image we can easily reproduce application environment and ensure consistency across deployments. 

## Nginx

NGINX is open source software for web serving, reverse proxying, caching, load balancing, media streaming, and more. Here we will be using it as a web server to deploy our static html 

Let's start with creating our own Dockerfile

## Creating Dockerfile
All docker images start from base image. The base images are used as foundation to which we can put additional changes to run the application. We can use any image availble on docker hub as base image. For adding base image we using below line in Dockerfile

```
FROM <image-name>:<tag>
```

For our purpose we will use ngnix image as the base image. So the first line of our Dockerfile will look like

```
FROM nginx:1.11-alpine
```

Once the base image is defined next step is to perform any kind of configuration that needs to be done in order for our application to run and copy the neccessary code to correct folder path in image. There are many commands used to configure image but main ones are COPY and RUN.

`RUN <command>` allows to execute any command in command prompt. The result of RUN are persisted in image.

`COPY <src> <dest>` allows to copy files from directory containing Dockerfile to container's image. Extremely useful to copy source code.

We will copy out static html code (make sure we have index.html file) to `/usr/share/nginx/html` path in container which is default path in nginx from which files are exposed. 

```
COPY . /usr/share/nginx/html
```

Next step is to expose the port. Our application will be running inside the container to expose it to the outside world we need to tell docker which port to keep open for connectivity. 

```
EXPOSE <port-numbers>
```

We can define either single or multiple ports to be exposed in single command. 

For our case we will expose port 80 which is defult port nginx listens requests to.

```
EXPOSE 80
```

After that we need to give the container default command. Default command is command that launches the application. The `CMD` line in Dockerfile defines the default command to run when container is launched.

```
CMD <command>
```

Alternate to `CMD` is `ENTRYPOINT`. While a CMD can be overriden when container starts a ENTRYPOINT defines command which can have arguments passed to it when container launches. 

For our case we will use CMD to start nginx server

```
CMD ["nginx", "-g", "daemon off;"]
```

After all this steps our Dockerfile looks something like this 

```
FROM nginx:1.11-alpine
COPY . /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

## Creating docker image

Now our Dockerfile is done we need to create a docker image out of it. To create docker image from Dockerfile we use `docker build` command. 

```
docker build -t <name:tag>
```

For building our image we will run following command in terminal in same folder as that of our static html file

```
docker build -t webserver-image:v1
```

Once this command is done by using `docker images` command we should be able to view our newly created image in the list. 

## Running docker image 
Now the next step is to run this image as form of container. To run image we use `docker run` command. 

```
docker run -d -p <external-port>:<docker-port> <image-id/image-name>
```

-d -> runs container in detached mode.
-p -> used to bind the port of container to the machine. 

For our image the command will be something like

```
docker run -d -p 80:80 webserver-image:v1
```

To view the output of running container we can either do curl command to check the webpage output or visit `localhost:80` on browser.

```
curl -i http://docker
```

which should show us output of static html

***

That’s all about creating a simple docker image for static html and deploying it !! I hope this article was able to clear the understanding of docker and docker image creation.

Happy Coding !!

## References
 
1. [Docker - Official Docs](https://docs.docker.com/engine/reference/commandline/image/)
