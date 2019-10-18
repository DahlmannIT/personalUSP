# Explanation

# Architecture

# Prerequisites

# Dockerfile

Dockerfiles describe how to assemble a private filesystem for a container, and can also contain some metadata describing how to run a container based on this image. The bulletin board app Dockerfile looks like this:

```yml
FROM node:6.11.5    

WORKDIR /usr/src/app
COPY package.json .
RUN npm install    
COPY . .

CMD [ "npm", "start" ]    
```

# Docker-compose
