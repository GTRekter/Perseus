---
layout: post
title: "Continuous testing and integration with GitHub repo and CircleCI"
subtitle: ""
description: "In this article, I’ll show you how to use two patterns that have penetrated all aspects of software development: continuous testing and integration."
excerpt: ""
date: 2020-04-09T02:09:16+09:00
author: "Ivan Porta"
image: ""
tags: ["CircleCI, Continuous Integration", "DevOps", "Software Development"]
URL: "/2020/04/09/continuous_testing_and_integration_with_github_repo_and_circleci/"
categories: [ Tech ]
---


In this article, I’ll show you how to use two patterns that have penetrated all aspects of software development: continuous testing and integration.

For this tutorial, I’m gonna use a repo on GitHub and CircliCI, so first, we need to sign up to CircleCI and link our GitHub profile.


# Create a new NPM project

Open the command prompt and initialize a new npm project by using the npm init command npm init.

```bash
    $ node init
    package name: (circle) cirlce
    version: (1.0.0) 1.0.0
    description: CircleCI continuous testing and integration
    entry point: (index.js) server.js
    test command: mocha test.js --recursive
    git repository:
    keywords:
    author: Ivan Porta
    license: (ISC) MIT
    About to write to C:\Users\ivanp\Desktop\Circle\package.json:
    Press ^C at any time to quit.
    {
        "name": "cirlce",
        "version": "1.0.0",
        "description": "CircleCI continuous testing and integration",
        "main": "server.js",
        "scripts": {
          "test": "mocha test.js --recursive"
        },
        "author": "Ivan Porta",
        "license": "MIT"
    }Is this OK? (yes) yes
```

Then create the server.js script and copy and paste the following code:

```js
    var express = require('express');
    var routerHandler = require("./routerHandler");
    var app = express();
    app.use("/message", routerHandler);
    app.get('/', function (req, res) {
          res.send('Hello World!');
    });
    app.listen(8080);console.log("Running on port 8080");
```

Now create a new file called test.js and paste the following code:

```js
    const assert = require("assert");
    const httpMocks = require("node-mocks-http");
    const routerHandler = require("./routerHandler");
    describe("Example Test 1", () => {
        it("should return 'Cortana 2.1' for GET /message", () => {
            
          const request = httpMocks.createRequest({
            method: "GET",
            url: "/message"
          });    const response = httpMocks.createResponse();
          routerHandler(request, response);
          const responseBody = response._getData();
          const expectedResponseBody = "Cortana 2.2!";    assert(responseBody, expectedResponseBody);
        });});
```

This script does nothing more than using the package node-mocks-http to compare the response from the relative URL /message to our expected string (in this case Cortana 2.2). In order to handle the route’s call I have created the following function in the file _routerHandler.js_:

```js
    function routerHandler (req, res) {
        res.send("Cortana 2.1!");
    }module.exports = routerHandler;
```

Now that our app is ready, create a new repository on push our code in it.


# Create a new Pipeline

Navigate to [https://circleci.com](https://circleci.com/) and click on the Sign-Up button on the top right corner of the page.

![](/images/continuous_testing_and_integration_with_github_repo_and_circleci/1.png)

The website will allow you to link both a Bitbucket and GitHub account. For the purpose of this demo, select the “Sign Up with GitHub” option.

![](/images/continuous_testing_and_integration_with_github_repo_and_circleci/2.png)

You will then be prompted to sign in to GitHub and then to the authorization page. After that click on _Authorize_ to be taken to the CircleCI dashboard. Click the option _Add Project_on the left menu to be redirected to a page with the list of repositories in your GitHub account and click the button _Set up Project_ on the right side of the raw related to your project.

![](/images/continuous_testing_and_integration_with_github_repo_and_circleci/3.png)

You will be redirected to a page that will show the initial setup for the selected project. In this case, I’m using a NodeJS project so I’ll select the Node option in the_ Language section._

![](/images/continuous_testing_and_integration_with_github_repo_and_circleci/4.png)

After you are done, click the _Start Building_ button on the right side of the page to confirm.

![](/images/continuous_testing_and_integration_with_github_repo_and_circleci/5.png)

CircleCI will give you the option to choose between letting it create a new branch and kicking off your pipeline automatically or letting you add the previous config file manually. In this case, I’ll be lazy and I’ll simply click _Add Config_.


# Testing phase

Before moving forward with the test phase I want to let you focus on the configuration file.

```yaml
    version: 2.1
    	orbs:
    	  node: circleci/node@1.1.6
    	jobs:
    	  build-and-test:
    	    executor:
    	      name: node/default
    	    steps:
    	      - checkout
    	      - node/with-cache:
    	          steps:
    	            - run: npm install
    	            - run: npm test
    	workflows:
    	    build-and-test:
    	      jobs:- build-and-test
```

The command run by npm test is gonna look for the script defined by the key _test_ in your _package.json_. For the purpose of this demo, I’m gonna use the JavaScript framework Mocha.

```json
    {
    	  ...
    	  "scripts": {
    	    "test": "mocha test.js --recursive"
    	  },
    	  ...
    }
```

Now its time to test our code. To do so, make some changes in the repo on your local machine and commit them. Once you have pushed the changes go back to your CircleCI page and click on the _Workflows_tab on the left menu to see that the test is being run on CircleCI.

![](/images/continuous_testing_and_integration_with_github_repo_and_circleci/6.png)
