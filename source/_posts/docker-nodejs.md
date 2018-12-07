---
title: Basic Docker Node.js Setup
date: 2018-12-06 23:24:07
tags:
    - docker
    - nodejs
    - guide
    - beginner
---

## Introduction

Docker is an amazing tool. It allows us to deploy quickly on any platform weather it be AWS, Google Cloud, Heroku, or basically any cloud provider without worry of complex install procedures. It also gets around the whole issue of "but it works on my machine" since the every developers "machine" is identical to each other and the server your app is deployed on. 

Here we'll be guiding you through getting docker working with a simple node.js application and pointing you in the right direction to learn more. 

## Getting set up

First of all your gonna need docker (duh). This process will change depending on what os you are using so I'll just link you to install directions below.

- [Ubuntu/Debian Linux](https://docs.docker.com/install/linux/docker-ce/ubuntu/) (The superior OS)

- [OSX](https://docs.docker.com/docker-for-mac/install/) (Linux but worse)

- [Windows](https://docs.docker.com/docker-for-windows/install/) (Seriously give [linux](https://linuxmint.com/) a shot it's the shit for programming)

## Dockerfile

Dockerfiles are the backbone of a docker based project. Think of it like a set up script that gives the instructions for building your container. Since we are making one for node.js we are going to use the `node:8.10.0-alpine` image as a base but you can change the 8.10.0 out for whatever version of node you happen to be using. So in your project base create a new file called `Dockerfile` with the following contents. 

```
FROM node:8.10.0-alpine
EXPOSE 3000

WORKDIR /app/
COPY package*.json .

RUN npm i

COPY src src

CMD [ "npm", "start" ]
```

If your a lazy fuck like I am you can just copy that and be done with it and continue reading [here](#Node-js-Application). But I'm going to go and explain each line of this file for the sake of the people out there who care. This is a guide after all. But if there's one key thing to learn here it's that we try to put all the commands that wont need to be re run at the top of the file. This is why we don't copy our src until the end of the file. This means that we wont have to run npm i each time we update our src files. If we just copied all the files and did npm i this would mean that we would have to re download ALL the npm modules each time we wanted to test our app. With this we only have to do that when we change package.json or package-lock.json.

---

```
FROM node:8.10.0-alpine
```

This line creates the docker container based off the image found [here](https://hub.docker.com/_/node/). This image comes with nodejs in a lightweight os called [alpine](https://alpinelinux.org/) that's basically only used for docker. 

---

```
EXPOSE 3000
```

This exposes port 3000 to allowing data to come in via this port. This can be set to whatever port you want you application to listen on. Pretty straightforward.

---

```
WORKDIR /app/
```

This sets the working directory for out docker application. You can think of it like a `cd` command but unlike that it will keep it's directory state for when the container starts up and runs our start command we set with CMD.

---
```
COPY package*.json .
```

This copies the two files that we need to install our npm modules that we need into our working directory `/app/` which is our current directory so we can refer to it with "." (. acts as the current directory and .. is the parent directory) and not have to use "/app". Having this separated from copying our src files means that we only have to re run the npm install command when we change those two files and we don't have to re run it when we change anything in src. This will make our builds a fuck of a lot faster.

---
```
RUN npm i
```

This runs the bash command npm i installing all our node modules. You can run anything here that you would be able to in the terminal.

---
```
COPY src src
```
This copies all our source files from our local machine to our docker image into the src directory within our working directory. So the the src directory will end up in /app/src since our working directory is currently /app.

---
```
CMD [ "npm", "start" ]
```


This defines how we want to start our docker container when we start it up. This is done in the format of `CMD [ "executable", "param1", "param2", ... ]`. So if we wanted to start our docker container with the command `java Main` we would get `CMD [ "java", "Main" ]`

---

## Node.js Application

### Package.json

For our application to run we don't need to have node.js installed on our machine but it couldn't hurt. If you want to get it follow one of the links below:

- [Linux/OSX](https://github.com/creationix/nvm) (I'd recommend getting nvm, it makes installing and switching node versions REALLY REALLY easy)
- [Windows](https://nodejs.org/en/download/) (Seriously just try linux, you can dual boot you don't need to give up windows)

Once we have that we want to create a simple package.json file. Just copy the text below into a new file in the same folder as the Dockerfile called `package.json`.

```json
{
  "name": "docker-nodejs",
  "version": "1.0.0",
  "dependencies": {
    "express": "^4.16.4"
  },
  "scripts": {
    "start": "node src/app.js"
  }
}
```

I'm going to gloss over what this file is doing and what it can do. Baiscly all you need to know is it defines a start command with `"start": "node src/app.js"` that we can then run with `npm start` and it states that we are going to need to download [express](https://expressjs.com/) to be able to run our application.

### The Actual Fucking Code

Hot damn we finally got here. We get to look at some mother fucking code finally. Copy the code below into a new file called `app.js` inside a new folder called src directory inside the root directory.

```javascript
const express = require('express');
const app = express();
const port = 3000;

app.get('/', (req, res) => {
    res.send('Sup Bitches');
});

app.listen(port, () => console.log(`App listening on port ${port}!`));
```

Wait what that's it? 9 fucking lines? Wow fuck this guide amiright.

Here's a breakdown of what's going on there. Skip to [here](#Building) if you could give less of a fuck.

---
```javascript
const express = require('express');
const app = express();
const port = 3000;
```

Line 1 imports the express library and stores it in the express variable. Then we create our app variable from that. Imagine that `app` is like our server or app. If we want to add a new endpoint or static file to our app we need to use this `app` thing. Then we just define a global constant for our port so we can easily change it later.

---
```javascript
app.get('/', (req, res) => {
    res.send('Sup Bitches');
});
```

This creates a new endpoint that accepts http GET requests. If we wanted to instead accept POST requests we could use `app.post(...` instead, same deal with any other http request type like PUT or DELETE. Then we define a function that takes in two parameters req and res that is called whenever a request is sent to the '/' endpoint. Then we use `res.send` to send a string to the client that made the request. 

---
```javascript
app.listen(port, () => console.log(`App listening on port ${port}!`));
```

This just starts the express server on the port we defined at the top of our code and we give the .listen function a function to call once it's done this. In our case this is just a friendly little print statement that lets us know our server is up and running.

---

## Building

Alright we got our app made now we get to the docker part of this bad boy. First you need to build your container to do this run the command below.

```bash
$ docker build -t test-app .
```

This command builds a new docker image with the name test-app in the current directory (represented by the "."). After the command has run we should have the docker image created you can check this by running: 

```bash
$ docker image ls
REPOSITORY                                     TAG                 IMAGE ID            CREATED             SIZE
test-app                                       latest              8d7fac3109e3        1 second ago        70.4MB
```

Cool cool, now that we have build our image we can run it with the command:

```bash
$ docker run -p 8080:3000 test-app

> docker-nodejs@1.0.0 start /app
> node src/app.js

App listening on port 3000!
```

Fuck yeah, but wait what's that 8080:3000 shit? So to keep it short we are able to route an port from the docker image to a completely different port on localhost. So that means that our code is actually wrong so we need to go to [localhost:8080](localhost:8080) to see our output not localhost:3000. You should see this in your browser:

![Sup Bitches](/assets/images/docker-nodejs/pic1.png)

Pro tip: if you don't want to have an extra terminal for just the docker container you can do this instead to detach the docker container from the current terminal (hence the -d flag)

```bash
$ docker run -p 8080:3000 -d test-app
```

Only downside to this is we cant see the logs, to do this we have to do as follows

```bash
$ docker run -p 8080:3000 -d test-app
b98d0968cf02324c8b667be517169ea95cdc1fed742f94d3ea44c237af4daf81

$ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                    NAMES
b98d0968cf02        test-app            "npm start"              7 seconds ago       Up 5 seconds        0.0.0.0:8080->3000/tcp   relaxed_agnesi

$ docker logs b98d0968cf02

> docker-nodejs@1.0.0 start /app
> node src/app.js

App listening on port 3000!
```

## Fin

Yay we done, this is the groundwork for any basic docker application using Node.js. If you want to take it to the next step with docker take a look at [docker-compose]() it's a nice lightweight docker composing tool. It basically just allows you to start up multiple containers that can talk with each other with a single command.

## Directory Structure

If you're running into issues getting this working here's the directory structure that everything needs to be in. If this doesn't work Google the error and solve it yourself. If that still doesn't work feel free to shoot me an email, if I'm feeling not lazy I'll help.

```
├── Dockerfile
├── node_modules
├── package.json
├── package-lock.json
└── src
    └── app.js
```