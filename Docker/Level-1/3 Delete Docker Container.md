### Task
A container named kke-container was created by one of the Nautilus project developers on App Server 2. It was solely for testing purposes and now requires deletion. Execute the following task:


Delete the kke-container on App Server 2 in Stratos DC.

### Solution
```
[steve@stapp02 ~]$ docker ps -a
CONTAINER ID        IMAGE               COMMAND               CREATED              STATUS              PORTS               NAMES
775bc42de618        busybox             "tail -f /dev/null"   About a minute ago   Up About a minute                       kke-container

[steve@stapp02 ~]$ docker stop $(docker ps -q)
775bc42de618

[steve@stapp02 ~]$ docker rm $(docker ps -aq)
775bc42de618

[steve@stapp02 ~]$ docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
```
