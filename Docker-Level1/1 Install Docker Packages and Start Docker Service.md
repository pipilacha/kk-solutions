###  Task

The Nautilus DevOps team aims to containerize various applications following a recent meeting with the application development team. They intend to conduct testing with the following steps:

1. Install docker-ce and docker compose packages on App Server 3.

2. Initiate the docker service.



### Solution

1. First let's check the type of system we are working on:
```
[banner@stapp03 ~]$ cat /etc/os-release 
NAME="CentOS Stream"
VERSION="8"
ID="centos"
ID_LIKE="rhel fedora"
VERSION_ID="8"
PLATFORM_ID="platform:el8"
PRETTY_NAME="CentOS Stream 8"
ANSI_COLOR="0;31"
CPE_NAME="cpe:/o:centos:centos:8"
HOME_URL="https://centos.org/"
BUG_REPORT_URL="https://bugzilla.redhat.com/"
REDHAT_SUPPORT_PRODUCT="Red Hat Enterprise Linux 8"
REDHAT_SUPPORT_PRODUCT_VERSION="CentOS Stream"
```
2. Let's follow Docker's installation guide for CentOs: https://docs.docker.com/engine/install/centos/

3. Finally let's start docker service
```
[banner@stapp03 ~]$ sudo systemctl start docker.service
[banner@stapp03 ~]$ sudo systemctl status docker
● docker.service - Docker Application Container Engine
   Loaded: loaded (/usr/lib/systemd/system/docker.service; disabled; vendor preset: disabled)
   Active: active (running) since Sun 2024-06-02 23:55:21 UTC; 4s ago
     Docs: https://docs.docker.com
 Main PID: 1683 (dockerd)
    Tasks: 20
   Memory: 39.3M
   CGroup: /docker/a32ee26b4cf424d0369b4222f5f206a24ad64ef813a84091d07f72f1955579c3/system.slice/docker.service
           └─1683 /usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock
```
