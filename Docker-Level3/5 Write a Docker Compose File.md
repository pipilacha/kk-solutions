### Task
The Nautilus application development team shared static website content that needs to be hosted on the httpd web server using a containerised platform. The team has shared details with the DevOps team, and we need to set up an environment according to those guidelines. Below are the details:


a. On App Server 3 in Stratos DC create a container named httpd using a docker compose file /opt/docker/docker-compose.yml (please use the exact name for file).

b. Use httpd (preferably latest tag) image for container and make sure container is named as httpd; you can use any name for service.

c. Map 80 number port of container with port 6200 of docker host.

d. Map container's /usr/local/apache2/htdocs volume with /opt/dba volume of docker host which is already there. (please do not modify any data within these locations).

### Solution
docker-compose.yml
```
name: httpd
services:
  httpd:
    container_name: httpd
    image: httpd:latest
    ports:
      - "6200:80"
    volumes:
      - /opt/dba:/usr/local/apache2/htdocs
```
```
banner@stapp03 ~]$ cd /opt/docker/

[banner@stapp03 docker]$ docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES

[banner@stapp03 docker]$ docker compose up --detach


[banner@stapp03 docker]$ docker ps -a
CONTAINER ID        IMAGE               COMMAND              CREATED             STATUS              PORTS                  NAMES
44a2c690d277        httpd:latest        "httpd-foreground"   8 seconds ago       Up 4 seconds        0.0.0.0:6200->80/tcp   httpd

[banner@stapp03 docker]$ docker exec -ti httpd bash
root@44a2c690d277:/usr/local/apache2# ls -la /usr/local/apache2/htdocs/
total 12
drwxr-xr-x  2 root     root     4096 Jun  3 21:02 .
drwxr-xr-x 12 www-data www-data 4096 May 14 02:57 ..
-rw-r--r--  1 root     root       34 Jun  3 21:01 index1.html
```
