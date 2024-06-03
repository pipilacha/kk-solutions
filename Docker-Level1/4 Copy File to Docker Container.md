### Task
The Nautilus DevOps team possesses confidential data on App Server 3 in the Stratos Datacenter. A container named ubuntu_latest is running on the same server.


Copy an encrypted file /tmp/nautilus.txt.gpg from the docker host to the ubuntu_latest container located at /usr/src/. Ensure the file is not modified during this operation.


### Solution
```
[banner@stapp03 ~]$ docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED              STATUS              PORTS               NAMES
e75d07833442        ubuntu              "/bin/bash"         About a minute ago   Up About a minute                       ubuntu_latest

[banner@stapp03 ~]$ docker cp --archive /tmp/nautilus.txt.gpg ubuntu_latest:/usr/src/
[banner@stapp03 ~]$ 
[banner@stapp03 ~]$ docker exec -ti ubuntu_latest bash
root@e75d07833442:/# ls -la /usr/src
total 12
drwxr-xr-x  2 root root 4096 Jun  3 15:31 .
drwxr-xr-x 12 root root 4096 Apr 29 14:02 ..
-rw-r--r--  1 root root  105 Jun  3 15:27 nautilus.txt.gpg
root@e75d07833442:/# exit
exit
[banner@stapp03 ~]$ ls -la /tmp/nautilus.txt.gpg 
-rw-r--r-- 1 root root 105 Jun  3 15:27 /tmp/nautilus.txt.gpg
```
