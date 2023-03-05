---
layout: post
title: "How to create a web application using NPM and Docker in 5 minutes or less"
subtitle: ""
description: "In this article, I will show first how to build a Docker image and then download it from Docker Hub and use it to instantiate a new web application."
excerpt: ""
date: 2020-03-09T02:09:16+09:00
author: "Ivan Porta"
image: ""
tags: ["NPM", "Docker", "Web Development", "Software Development", "Containers"]
URL: "/2020/03/09/how_to_create_a_web_application_using_npm_and_docker_in_5_minutes_or_less/images/"
categories: [ Tech ]
---


In this article, I will show first how to build a Docker image and then download it from Docker Hub and use it to instantiate a new web application.


# Requirements

## Windows

- Windows 10 64-bit: Pro, Enterprise, or Education (Build 15063 or later).
- Hyper-V and Containers Windows features must be enabled.

## Linux

- You are fine ;-)


# Installation

For this exercise, we will need to use the Docker platform. To do so we have to create a new account on Docker Hub (a sort of GitHub for docker images).


## Windows and Mac

1. Navigate to <https://hub.docker.com/> and create a new account
2. Click **Explore**on the right side of the page
3. Select the filter **Docker CE (CE stands for Community Edition)**
4. Search for **Docker Desktop for Windows** and then install it

![](/images/how_to_create_a_web_application_using_npm_and_docker_in_5_minutes_or_less/images/1.png)


## Linux

1. Navigate to <https://hub.docker.com/> and create a new account
2. Click **Explore**on the right side of the page
3. Select the filter **Docker CE (CE stand for Community Edition)**
4. Search for **Docker Engine — Ubuntu (Community)** and then install it

![](/images/how_to_create_a_web_application_using_npm_and_docker_in_5_minutes_or_less/2.png)

Once the installation is over (Windows users need to restart their machine before moving forward) check if everything went well by executing the command:

```bash
    docker --version
```

This command will prompt an output message with the version and build number of your docker

```bash
    Docker version 19.03.5, build 633a0ea
```

# Create a new npm package

Let’s create a new folder (the place where we will put all files related to our web application) and name it _CoolWebsite_. Inside the folder create a new Javascript file called server.js and paste the following code, which will have our web application in it.

```js
    var express = require('express');
    var app = express();
    app.get('/', function(req, res) {
        res.send('<h1>Welcome to my Docker Tutorial</h1>');
    });
    app.get('/random', function(req, res) {
          var num = Math.floor(Math.random() * 3);
          res.send('<h1>My lucky number =  ' + num +'</h1>');
    });
    app.listen(8080);
    console.log("Running on port 8080");
```

Once you have done this, it’s time to generate our _package.json_file by executing the command:

```bash
    npm init
```

Its execution will prompt you to make several inputs for a few aspects of the project like the project’s name, version, etc.

```bash
    $ npm init
    This utility will walk you through creating a package.json file.
    It only covers the most common items, and tries to guess sensible defaults.
    See `npm help json` for definitive documentation on these fields
    and exactly what they do.
    Use `npm install <pkg>` afterwards to install a package and
    save it as a dependency in the package.json file.
    Press ^C at any time to quit.
    package name: (coolwebsite) coolwebsite
    version: (1.0.0) 1.0.0
    description: Docker tutorial
    entry point: (server.js) server.js
    test command:
    git repository:
    keywords: Docker
    author: Ivan Porta
    license: (ISC) ISC
    About to write to C:\Users\ivanp\Desktop\CoolWebsite\package.json:
    {
        "name": "coolwebsite",
        "version": "1.0.0",
        "description": "Docker tutorial",
        "main": "server.js",
        "scripts": {
          "test": "echo \"Error: no test specified\" && exit 1",
          "start": "node server.js"
        },
        "keywords": [
          "Docker"
        ],
        "author": "Ivan Porta",
        "license": "ISC"
    }Is this OK? (yes) yes
```

Once the file is generated, create a new file called _Dockerfile_ and paste the following code in it:

```bash
FROM node:carbon 
WORKDIR /usr/src/app 
COPY . . 
RUN npm install 
EXPOSE 8080 
CMD ["npm", "start"]
```

The only purpose of the lines above is to tell docker what layers we’ll need and what commands need to be run when the application is run via docker. For example, our file will first download an image for Node, define a WORKDIR in the container, copy package.json into the container, execute npm install to pull in the libraries, copy the files from our directory to the container, expose port 8080, and lastly runs the command npm start which is defined in the package.json file.


# Build an image

Now that we have a Dockerfile, we need to build an image by following:

```bash
docker build -t coolwebsite ./
```

The parameter _-t_ express the name that we are giving the image.


# Run an image locally

In order to run this image on our local machine, let’s create a live running container by executing the following command:

```bash
docker run -p 3001:8080 coolwebsite
```

The parameter _-p_ maps a port of our local machine to a port of our container (in this case, maps port 3001 of our local machine to port 8080 of the container)

To see the results enter the URL localhost:3001 into the browser.


# Store this Image on Docker Hub

It’s time to upload our container image onto Docker Hub, to do so we have to log in to Docker Hub by following:

```bash
docker login --username=your_dockerhub_username
```

Then look for the Image ID for the image that you want to send to this repository by using the command:

```bash
docker images
```

Link the image to the one that will be stored on Docker hub using the tagging system:

```bash
docker tag ImageID your_dockerhub_username/name_of_repo
```

And then push the built image to your Docker Hub, by executing the command:

```bash
docker push your_dockerhub_username/name_of_repo
```

Now that our image has been uploaded to Docker Hub, we can treat it in the same way as the local image. To see our code running from a container we must execute the following command:

```bash
docker run your_dockerhub_username/name_of_repo
```
