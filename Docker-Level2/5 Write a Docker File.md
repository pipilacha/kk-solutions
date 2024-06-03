### Task
As per recent requirements shared by the Nautilus application development team, they need custom images created for one of their projects. Several of the initial testing requirements are already been shared with DevOps team. Therefore, create a docker file /opt/docker/Dockerfile (please keep D capital of Dockerfile) on App server 1 in Stratos DC and configure to build an image with the following requirements:


a. Use ubuntu as the base image.

b. Install apache2 and configure it to work on 5002 port. (do not update any other Apache configuration settings like document root etc).

### Solution
Dockerfile content
```
FROM ubuntu:latest

RUN apt-get update > /dev/null && apt-get install -y apache2 > /dev/null

RUN sed -i 's/80/5002/' /etc/apache2/ports.conf > /dev/null

EXPOSE 5002

CMD apachectl -D FOREGROUND                                    
```
```
[tony@stapp01 ~]$ cd /opt/docker/

[tony@stapp01 docker]$ sudo vi Dockerfile

[tony@stapp01 docker]$ docker build --tag ubuntu:apache2 .
Sending build context to Docker daemon  2.048kB
Step 1/5 : FROM ubuntu:latest
latest: Pulling from library/ubuntu
49b384cc7b4a: Pull complete 
Digest: sha256:3f85b7caad41a95462cf5b787d8a04604c8262cdcdf9a472b8c52ef83375fe15
Status: Downloaded newer image for ubuntu:latest
 ---> bf3dc08bfed0
Step 2/5 : RUN apt-get update > /dev/null && apt-get install -y apache2 > /dev/null
 ---> Running in ddb1b1507108
debconf: delaying package configuration, since apt-utils is not installed
Removing intermediate container ddb1b1507108
 ---> 591c88f5f37e
Step 3/5 : RUN sed -i 's/80/5002/' /etc/apache2/ports.conf > /dev/null
 ---> Running in 29109e7d8fc5
Removing intermediate container 29109e7d8fc5
 ---> 1406427b9813
Step 4/5 : EXPOSE 5002
 ---> Running in eb4d69d34179
Removing intermediate container eb4d69d34179
 ---> 23a87a8afd48
Step 5/5 : CMD apachectl -D FOREGROUND
 ---> Running in 0c63e3e7e364
Removing intermediate container 0c63e3e7e364
 ---> 7e7c65202b01
Successfully built 7e7c65202b01
Successfully tagged ubuntu:apache2
```

Testing
```
[tony@stapp01 docker]$ docker run --rm -d -p 80:5002 ubuntu:apache2
f767908183d3a5be5d5cc3ac4101237664230da736cc66ba098229e2c09cf3b0
[tony@stapp01 docker]$ curl localhost:80
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml">
  <!--
    Modified from the Debian original for Ubuntu
    Last updated: 2022-03-22
    See: https://launchpad.net/bugs/1966004
  -->
...
```
