### Task
The Nautilus DevOps team is conducting application deployment tests on selected application servers. They require a nginx container deployment on Application Server 2. 

Complete the task with the following instructions:
* On Application Server 2 create a container named nginx_2 using the nginx image with the alpine tag. Ensure container is in a running state.


### Solution
1. Let's check existing containers
 ```
    [steve@stapp02 ~]$ docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
```

2. Lets run the desired container.
    * Will map ports 80:80 as it is not specified.
    * Will run in detach mode
    * There is no volume requirement
```
[steve@stapp02 ~]$ docker run --name nginx_2 -p 80:80 -d nginx:alpine 
Unable to find image 'nginx:alpine' locally
alpine: Pulling from library/nginx
4abcf2066143: Pull complete 
b1e69ebc7f92: Pull complete 
628158b45bce: Pull complete 
346e52e95fa0: Pull complete 
8c57fb1cd644: Pull complete 
dc3800d1d0f2: Pull complete 
e3227d68030d: Pull complete 
8c50e1264d11: Pull complete 
Digest: sha256:69f8c2c72671490607f52122be2af27d4fc09657ff57e42045801aa93d2090f7
Status: Downloaded newer image for nginx:alpine
715d5e07629abbb353e31bb81140a3a9cd6cdae5c11dbc756520e796dea8a7a9
[steve@stapp02 ~]$ docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS              PORTS                NAMES
715d5e07629a        nginx:alpine        "/docker-entrypoint.…"   About a minute ago   Up About a minute   0.0.0.0:80->80/tcp   nginx_2
```   
