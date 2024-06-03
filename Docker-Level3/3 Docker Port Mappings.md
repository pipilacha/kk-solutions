### Task
The Nautilus DevOps team is planning to host an application on a nginx-based container. There are number of tickets already been created for similar tasks. One of the tickets has been assigned to set up a nginx container on Application Server 2 in Stratos Datacenter. Please perform the task as per details mentioned below:

a. Pull nginx:stable docker image on Application Server 2.

b. Create a container named games using the image you pulled.

c. Map host port 8085 to container port 80. Please keep the container in running state.

### Solution
```
[steve@stapp02 ~]$ docker run --name games -d -p 8085:80 nginx:stable
Unable to find image 'nginx:stable' locally
stable: Pulling from library/nginx
09f376ebb190: Pull complete 
50cd203f2871: Pull complete 
9b3addd3eb3d: Pull complete 
c2744bd3c388: Pull complete 
99c7eef6601a: Pull complete 
0b362abff7c0: Pull complete 
f9e3483e2126: Pull complete 
Digest: sha256:ec881e8e2b068e388e992e0e12360ff9f93737f90eb3e346d0ceac83710383d8
Status: Downloaded newer image for nginx:stable
82d4a8125c0c4b002414c5c4144c7a632f0b6714746bba56ff34794641097ce9

[steve@stapp02 ~]$ docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                  NAMES
82d4a8125c0c        nginx:stable        "/docker-entrypoint.…"   20 seconds ago      Up 6 seconds        0.0.0.0:8085->80/tcp   games
```
