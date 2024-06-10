### Task
There is a requirement to Dockerize a Node app and to deploy the same on App Server 3. Under /node_app directory on App Server 3, we have already placed a package.json file that describes the app dependencies and server.js file that defines a web app framework.


    Create a Dockerfile (name is case sensitive) under /node_app directory:
        Use any node image as the base image.
        Install the dependencies using package.json file.
        Use server.js in the CMD.
        Expose port 8083.

    The build image should be named as nautilus/node-web-app.

    Now run a container named nodeapp_nautilus using this image.
        Map the container port 8083 with the host port 8099.

. Once deployed, you can test the app using a curl command on App Server 3:


curl http://localhost:8099


### Solution
```
[banner@stapp03 ~]$ cd /node_app/
[banner@stapp03 node_app]$ ls -la
total 16
drwxr-xr-x 2 root root 4096 Jun 10 01:57 .
drwxr-xr-x 1 root root 4096 Jun 10 01:57 ..
-rw-r--r-- 1 root root  332 Jun 10 01:57 package.json
-rw-r--r-- 1 root root  296 Jun 10 01:57 server.js

[banner@stapp03 node_app]$ sudo vi Dockerfile
# Docker file content
FROM node:latest

ADD . /app

WORKDIR /app

RUN npm install

EXPOSE 8083

CMD node server.js

[banner@stapp03 node_app]$ docker build -t nautilus/node-web-app .
Sending build context to Docker daemon  4.096kB
Step 1/6 : FROM node:latest
 ---> 3d4b037e6712
Step 2/6 : ADD . /app
 ---> 364825fd5013
Step 3/6 : WORKDIR /app
 ---> Running in f5bf33b6d979
Removing intermediate container f5bf33b6d979
 ---> f52e0cea337e
Step 4/6 : RUN npm install
 ---> Running in 0f57253d7c7d

added 64 packages, and audited 65 packages in 3s

12 packages are looking for funding
  run `npm fund` for details

found 0 vulnerabilities
npm notice
npm notice New minor version of npm available! 10.7.0 -> 10.8.1
npm notice Changelog: https://github.com/npm/cli/releases/tag/v10.8.1
npm notice To update run: npm install -g npm@10.8.1
npm notice
Removing intermediate container 0f57253d7c7d
 ---> 9db0075ab740
Step 5/6 : EXPOSE 8083
 ---> Running in 579eb34fa928
Removing intermediate container 579eb34fa928
 ---> 4d2a6965ad69
Step 6/6 : CMD node server.js
 ---> Running in b61325f62cc9
Removing intermediate container b61325f62cc9
 ---> 1d9af39f0994
Successfully built 1d9af39f0994
Successfully tagged nautilus/node-web-app:latest

[banner@stapp03 node_app]$ docker run --name nodeapp_nautilus --rm -d -p 8099:8083 nautilus/node-web-app
e84a948a5501ada142b5f9ad421d0c8c15770f62bbb194834f49f107d4ae946e

[banner@stapp03 node_app]$ docker ps
CONTAINER ID        IMAGE                   COMMAND                  CREATED             STATUS              PORTS                    NAMES
e84a948a5501        nautilus/node-web-app   "docker-entrypoint.s…"   59 seconds ago      Up 7 seconds        0.0.0.0:8099->8083/tcp   nodeapp_nautilus

[banner@stapp03 node_app]$ curl localhost:8099
Welcome to xFusionCorp Industries!
```
