### Task
The Nautilus DevOps team is working to create new images per requirements shared by the development team. One of the team members is working to create a Dockerfile on App Server 2 in Stratos DC. While working on it she ran into issues in which the docker build is failing and displaying errors. Look into the issue and fix it to build an image as per details mentioned below:

a. The Dockerfile is placed on App Server 2 under /opt/docker directory.

b. Fix the issues with this file and make sure it is able to build the image.

c. Do not change base image, any other valid configuration within Dockerfile, or any of the data been used — for example, index.html.

Note: Please note that once you click on FINISH button all existing images, the containers will be destroyed and new image will be built from your Dockerfile.

### Solution
```
[steve@stapp02 ~]$ cd /opt/docker/
[steve@stapp02 docker]$ ls -la
total 20
drwxrwxrwx 4 root root 4096 Jun  6 19:40 .
drwxr-xr-x 1 root root 4096 Jun  6 19:40 ..
-rw-r--r-- 1 root root  523 Jun  6 19:39 Dockerfile
drwxr-xr-x 2 root root 4096 Jun  6 18:34 certs
drwxr-xr-x 2 root root 4096 Jun  6 18:34 html
[steve@stapp02 docker]$ cat Dockerfile 
FROM httpd:2.4.43

RUN sed -i "s/Listen 80/Listen 8080/g" /usr/local/apache2/conf/httpd.conf

RUN sed -i '/LoadModule\ ssl_module modules\/mod_ssl.so/s/^#//g' conf/httpd.conf

RUN sed -i '/LoadModule\ socache_shmcb_module modules\/mod_socache_shmcb.so/s/^#//g' conf/httpd.conf

RUN sed -i '/Include\ conf\/extra\/httpd-ssl.conf/s/^#//g' conf/httpd.conf

RUN cp certs/server.crt /usr/local/apache2/conf/server.crt

RUN cp certs/server.key /usr/local/apache2/conf/server.key

RUN cp html/index.html /usr/local/apache2/htdocs/


[steve@stapp02 docker]$ docker build .
Sending build context to Docker daemon  9.216kB
Step 1/8 : FROM httpd:2.4.43
2.4.43: Pulling from library/httpd
bf5952930446: Pull complete 
3d3fecf6569b: Pull complete 
b5fc3125d912: Pull complete 
3c61041685c0: Pull complete 
34b7e9053f76: Pull complete 
Digest: sha256:cd88fee4eab37f0d8cd04b06ef97285ca981c27b4d685f0321e65c5d4fd49357
Status: Downloaded newer image for httpd:2.4.43
 ---> f1455599cc2e
Step 2/8 : RUN sed -i "s/Listen 80/Listen 8080/g" /usr/local/apache2/conf/httpd.conf
 ---> Running in 214baffdc65b
Removing intermediate container 214baffdc65b
 ---> 7aa677173846
Step 3/8 : RUN sed -i '/LoadModule\ ssl_module modules\/mod_ssl.so/s/^#//g' conf/httpd.conf
 ---> Running in 94981d860f7b
Removing intermediate container 94981d860f7b
 ---> fcbcf8ec849a
Step 4/8 : RUN sed -i '/LoadModule\ socache_shmcb_module modules\/mod_socache_shmcb.so/s/^#//g' conf/httpd.conf
 ---> Running in 55da2dbca9fc
Removing intermediate container 55da2dbca9fc
 ---> 05687b4a0d6f
Step 5/8 : RUN sed -i '/Include\ conf\/extra\/httpd-ssl.conf/s/^#//g' conf/httpd.conf
 ---> Running in 406bb19765cb
Removing intermediate container 406bb19765cb
 ---> f06159c4d8eb
Step 6/8 : RUN cp certs/server.crt /usr/local/apache2/conf/server.crt
 ---> Running in 334d6526c226
cp: cannot stat 'certs/server.crt': No such file or directory
The command '/bin/sh -c cp certs/server.crt /usr/local/apache2/conf/server.crt' returned a non-zero code: 1


# The issue is they are trying to copy a file on host to image using RUN. COPY instruction should be used instead

[steve@stapp02 docker]$ cat Dockerfile 
FROM httpd:2.4.43

RUN sed -i "s/Listen 80/Listen 8080/g" /usr/local/apache2/conf/httpd.conf

RUN sed -i '/LoadModule\ ssl_module modules\/mod_ssl.so/s/^#//g' conf/httpd.conf

RUN sed -i '/LoadModule\ socache_shmcb_module modules\/mod_socache_shmcb.so/s/^#//g' conf/httpd.conf

RUN sed -i '/Include\ conf\/extra\/httpd-ssl.conf/s/^#//g' conf/httpd.conf

COPY certs/server.crt /usr/local/apache2/conf/server.crt

COPY certs/server.key /usr/local/apache2/conf/server.key

COPY html/index.html /usr/local/apache2/htdocs/

[steve@stapp02 docker]$ docker image ls
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE

[steve@stapp02 docker]$ docker build .
Sending build context to Docker daemon  9.216kB
Step 1/8 : FROM httpd:2.4.43
2.4.43: Pulling from library/httpd
bf5952930446: Already exists 
3d3fecf6569b: Already exists 
b5fc3125d912: Already exists 
3c61041685c0: Already exists 
34b7e9053f76: Already exists 
Digest: sha256:cd88fee4eab37f0d8cd04b06ef97285ca981c27b4d685f0321e65c5d4fd49357
Status: Downloaded newer image for httpd:2.4.43
 ---> f1455599cc2e
Step 2/8 : RUN sed -i "s/Listen 80/Listen 8080/g" /usr/local/apache2/conf/httpd.conf
 ---> Running in f82eaaa5ff29
Removing intermediate container f82eaaa5ff29
 ---> 1cc85596853d
Step 3/8 : RUN sed -i '/LoadModule\ ssl_module modules\/mod_ssl.so/s/^#//g' conf/httpd.conf
 ---> Running in 01e5652c08c7
Removing intermediate container 01e5652c08c7
 ---> 230dabe51311
Step 4/8 : RUN sed -i '/LoadModule\ socache_shmcb_module modules\/mod_socache_shmcb.so/s/^#//g' conf/httpd.conf
 ---> Running in b05db870f726
Removing intermediate container b05db870f726
 ---> caff8a440e15
Step 5/8 : RUN sed -i '/Include\ conf\/extra\/httpd-ssl.conf/s/^#//g' conf/httpd.conf
 ---> Running in 43708f4b4dad
Removing intermediate container 43708f4b4dad
 ---> 300664700c04
Step 6/8 : COPY certs/server.crt /usr/local/apache2/conf/server.crt
 ---> 098c884e21fa
Step 7/8 : COPY certs/server.key /usr/local/apache2/conf/server.key
 ---> 81e16c03c949
Step 8/8 : COPY html/index.html /usr/local/apache2/htdocs/
 ---> bbf6d8759eaa
Successfully built bbf6d8759eaa

[steve@stapp02 docker]$ docker image ls
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
<none>              <none>              bbf6d8759eaa        5 seconds ago       166MB
httpd               2.4.43              f1455599cc2e        3 years ago         166MB

```
