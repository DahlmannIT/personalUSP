# Explanation

# Architecture

# Prerequisites

# Dockerfile

Dockerfiles describe how to assemble a private filesystem for a container, and can also contain some metadata describing how to run a container based on this image. The bulletin board app Dockerfile looks like this:

```sql
FROM node:6.11.5    

WORKDIR /usr/src/app
COPY package.json .
RUN npm install    
COPY . .

CMD [ "npm", "start" ]    
```

Writing a Dockerfile is the first step to containerizing an application. You can think of these Dockerfile commands as a step-by-step recipe on how to build up our image. This one takes the following steps:

  - Start `FROM` the pre-existing `node:6.11.5` image. This is an official image, built by the node.js vendors and validated by Docker to be a high-quality image containing the node 6.11.5 interpreter and basic dependencies.
  - Use `WORKDIR` to specify that all subsequent actions should be taken from the directory `/usr/src/app` in your image filesystem (never the host’s filesystem).
  - `COPY` the file `package.json` from your host to the present location (`.`) in your image (so in this case, to /usr/src/app/package.json`)
  - `RUN` the command `npm install` inside your image filesystem (which will read `package.json` to determine your app’s node dependencies, and install them)
  - `COPY` in the rest of your app’s source code from your host to your image filesystem.
  
The steps above built up the filesystem of our image, but there’s one more line in our Dockerfile. The `CMD` directive is our first example of specifying some metadata in our image that describes how to run a container based off of this image. In this case, it’s saying that the containerized process that this image is meant to support is `npm start`.

# Docker-compose
