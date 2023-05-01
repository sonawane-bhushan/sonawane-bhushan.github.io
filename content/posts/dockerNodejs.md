---
title: "Dockerising Node.js Application"
date: 2023-04-30T11:30:03+00:00
weight: 1
aliases: ["/docker-nodejs"]
tags: ["docker", "nodejs", "beginners"]
author: "Bhushan Sonawane"
showToc: true
TocOpen: false
draft: false
hidemeta: false
comments: true
description: "How to create a Docker Image for node js based application"
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

In this blog we will take a look how to dockerise a node.js application. In last blog we saw how we can create a docker image for static html pages. Checkout that blog [here](https://sonawane-bhushan.github.io/posts/dockerstatichtmlnginx/) if you haven't as it also explains basics of docker and docker images in general which will be helpful here. 

This blog is a beginners guide on how to create a docker image for node.js based application. As we all know node.js is widely popular and used in creating both frontend as well as backend components, this blog will help us understand way to dockerise those applications.

As we saw last time to run a application in docker we need a docker image which is a standalone, executable package that contains everything needed to run an application, including the code, dependencies, and system libraries. It is created from Dockerfile which is is like a receipe to create the docker image. It contains series of instructions that tell docker how to create an image, such as what base image to use, which command to run, which files to include, how to setup environment.

So we will start with Dockerfile to dockerise node.js application. Let's dive into it 

## Base Image
As we know from the last blog the first line in Dockerfile contains the base image over which we run additional commands copy codebase and configure it to run our application. For our node.js application we will be using `node:10-alpine` image. 

```
FROM node:10-alpine
```

## Creating folder structure and defining WORKDIR

Now we are done with defining our base image the next step is to create directory structure before copying the codebase in. For creating directory structure we will use `RUN` command. Our directory structure will look something like this `/src/app`. 

```
RUN mkdir -p /src/app
```

Next we will define app folder as our WORKDIR. WORKDIR is a directory over which all the commands will be executed. 

```
WORKDIR /src/app
```

## npm install

For node.js application we know we have something called as `package.json` file which contains all the dependencies required to run that application. We need to install all those dependencies before we will be able to run our application. 

Docker provides a caching mechanism while creating images which will only execute steps in Dockerfile if something has been changed related to that. Since most of the times content of `package.json` stays unchanged we can take advantage of this caching to decrease build time required to create the image. To do this we will just copy `package.json` file to our WORKDIR and then run `npm install` to install all required dependancies and then we will copy the codebase seperatly. This will only run `npm install` if anything in `package.json` is changed otherwise it will keep using cached data. 

```
COPY package.json /src/app/package.json
RUN npm install
```

## Configuring Application

Now we have installed all the node dependancies required to run the application next step is to copy the source code and configure the application. We will add below line to copy the source code

```
COPY . /src/app
```

Next we want to expose the port to allow outside world to connect to container over that port where node application will run. Since our application is running on port 3000 we will expose that port 

```
EXPOSE 3000
```

We will run our application using `npm start` command 

```
CMD npm start
```

## Dockerfile

After adding all the above steps to Dockerfile it looks something like this 

```
FROM node:10-alpine
RUN mkdir -p /src/app
WORKDIR /src/app
COPY package.json /src/app/package.json
RUN npm install
COPY . /src/app
EXPOSE 3000
CMD npm start
```

## Building and Launching Application

We will create image for our node.js application with `docker build` command

```
docker build -t my-node-js-app .
```

To run this image as a container and expose it to port 3000 we will use following command

```
docker run -d --name my-app -p 3000:3000 my-node-js-app
```

## Environment Variables

Sometime applications need environment variables at time of start and Docker images must be designed in a way so that they can be transferred from one environment to another without changes required to rebuilt. With docker environment variables can be defined when you launch a container eg. with node.js application you should set `NODE_ENV` to production when running in production environment. 

Using `-e` option in `docker run` command we can set in environment variables to the container. 

```
docker run -d --name my-prod-app -p 3000:3000 my-node-js-app -e NODE_ENV=production
```

Once the docker run is completed we can access our node js application on port 3000 on browser. Try hitting localhost:3000 to see node js application in action

***

That’s all about creating a docker image for node js !! I hope this article was able to clear the understanding of docker and docker image creation.

Happy Coding !!

## References
 
1. [Docker - Official Docs](https://docs.docker.com/engine/reference/commandline/image/)
