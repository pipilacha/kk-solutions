### Task
The Nautilus DevOps team is working to deploy one of the applications on App Server 1 in Stratos DC. Due to a misconfiguration in the docker compose file, the deployment is failing. We would like you to take a look into it to identify and fix the issues. More details can be found below:

a. docker-compose.yml file is present on App Server 1 under /opt/docker directory.

b. Try to run the same and make sure it works fine.

c. Please do not change the container names being used. Also, do not update or alter any other valid config settings in the compose file or any other relevant data that can cause app failure.

Note: Please note that once you click on FINISH button all existing running/stopped containers will be destroyed, and your compose will be run.

### Solution
```
[tony@stapp01 ~]$ cd /opt/docker/

[tony@stapp01 docker]$ ls -la
total 16
drwxrwxrwx 3 root root 4096 Jun  9 22:05 .
drwxr-xr-x 1 root root 4096 Jun  9 22:05 ..
drwxrwxrwx 2 root root 4096 Jun  9 22:06 app
-rw-r--r-- 1 root root  262 Jun  9 22:03 docker-compose.yml

[tony@stapp01 docker]$ cat docker-compose.yml 
name: myapp

service:
    web:
        build: /app
        container_name: python
        ports:
            - "5000:5000"
        volumes:
            - .:/code
        depends_on:
            - redis
    redis:
        from: redis
        container_name: redis

[tony@stapp01 docker]$ docker compose up
validating /opt/docker/docker-compose.yml: (root) Additional property service is not allowed

# root property service should be services.
[tony@stapp01 docker]$ docker compose up
validating /opt/docker/docker-compose.yml: (root) Additional property service is not allowed

# for redis container, the image is specfied using image not from
[tony@stapp01 docker]$ docker compose up
validating /opt/docker/docker-compose.yml: services.redis Additional property from is not allowed

# for web container, we need to fix the location of the Dockerfile for the build property
[tony@stapp01 docker]$ docker compose up
[+] Running 9/9
 ✔ redis Pulled                                                                                       11.5s 
   ✔ 09f376ebb190 Pull complete                                                                        4.9s 
   ✔ 9ae6a7172b01 Pull complete                                                                        5.6s 
   ✔ 2c310454138b Pull complete                                                                        6.2s 
   ✔ 3eba9ec960aa Pull complete                                                                        7.0s 
   ✔ 3d36c165ff0a Pull complete                                                                        8.6s 
   ✔ 493d196d734f Pull complete                                                                        9.3s 
   ✔ 4f4fb700ef54 Pull complete                                                                       10.0s 
   ✔ 484e0560ae90 Pull complete                                                                       10.6s 
[+] Building 0.0s (0/0)                                                                      docker:default
unable to prepare context: path "/app" not found

# Another issue is going to be the volume property.
# We are mounting /opt/docker to /code inside the container.
# This will hide the existing content in /code which are the files our pyhon app nees to run.
# The possible solutions are:
#   1. Delete the volume if it is not required.
#   2. Mount at another location so it doesnt overrides /code
# Here since there is no specific mention of the mount I will just delete it.

# docker-compose.yml with the corrections:

[tony@stapp01 docker]$ cat docker-compose.yml 
name: myapp
services:
    web:
        build: ./app
        container_name: python
        ports:
            - "5000:5000"
        depends_on:
            - redis
    redis:
        image: redis
        container_name: redis
```
