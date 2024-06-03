### Task
One of the Nautilus developer was working to test new changes on a container. He wants to keep a backup of his changes to the container. A new request has been raised for the DevOps team to create a new image from this container. Below are more details about it:

a. Create an image news:devops on Application Server 3 from a container ubuntu_latest that is running on same server.


### Solution
```
[banner@stapp03 ~]$ docker image ls
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
ubuntu              latest              bf3dc08bfed0        5 weeks ago         76.2MB

[banner@stapp03 ~]$ docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED              STATUS              PORTS               NAMES
7f68f63c91a5        ubuntu              "/bin/bash"         About a minute ago   Up 59 seconds                           ubuntu_latest

[banner@stapp03 ~]$ docker commit ubuntu_latest news:devops
sha256:598977bc70fa0b8dbcf0e8ee808507a1c43bd0bc6ace5b301f77946328e940f1

[banner@stapp03 ~]$ docker image ls
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
news                devops              598977bc70fa        4 seconds ago       112MB
ubuntu              latest              bf3dc08bfed0        5 weeks ago         76.2MB
```
