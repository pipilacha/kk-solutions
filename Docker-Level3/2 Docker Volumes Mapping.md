### Task
The Nautilus DevOps team is testing applications containerization, which is supposed to be migrated on docker container-based environments soon. In today's stand-up meeting one of the team members has been assigned a task to create and test a docker container with certain requirements. Below are more details:

a. On App Server  2 in Stratos DC pull nginx image (preferably latest tag but others should work too).

b. Create a new container with name official from the image you just pulled.

c. Map the host volume /opt/finance with container volume /home. There is an sample.txt file present on same server under /tmp; copy that file to /opt/finance. Also please keep the container in running state.

### Solution
```
[steve@stapp02 ~]$ docker run --name official -d -v /opt/finance/:/home/ nginx
a95613a22fd909b3ad56952166e5350d9f91c820ed9b2c23fbe5d3a839fb895b

[steve@stapp02 ~]$ sudo cp /tmp/sample.txt /opt/finance/

[steve@stapp02 ~]$ ls -la /opt/finance/
total 12
drwxr-xr-x 2 root root 4096 Jun  3 19:46 .
drwxr-xr-x 1 root root 4096 Jun  3 19:40 ..
-rw-r--r-- 1 root root   23 Jun  3 19:46 sample.txt

[steve@stapp02 ~]$ docker exec -ti official bash
root@05b683418e24:/# ls -la /home/
total 12
drwxr-xr-x  2 root root 4096 Jun  3 19:46 .
drwxr-xr-x 18 root root 4096 Jun  3 19:48 ..
-rw-r--r--  1 root root   23 Jun  3 19:46 sample.txt
```
