### Task
One of the DevOps team members was working on to create a new custom docker image on App Server 1 in Stratos DC. He is done with his changes and image is saved on same server with name cluster:devops. Recently a requirement has been raised by a team to use that image for testing, but the team wants to test the same on App Server 3. So we need to provide them that image on App Server 3 in Stratos DC.

a. On App Server 1 save the image blog:devops in an archive.

b. Transfer the image archive to App Server 3.

c. Load that image archive on App Server 3 with same name and tag which was used on App Server 1.

Note: Docker is already installed on both servers; however, if its service is down please make sure to start it.


### Solution
```
[tony@stapp01 ~]$ docker image save cluster:devops > cluster_devops.tar

[tony@stapp01 ~]$ tar -tf cluster_devops.tar 
0d837ac702e072184aa9c50dcb5f93932b65dc0aa98a0dd74192cd537cdb8f71/
0d837ac702e072184aa9c50dcb5f93932b65dc0aa98a0dd74192cd537cdb8f71/VERSION
0d837ac702e072184aa9c50dcb5f93932b65dc0aa98a0dd74192cd537cdb8f71/json
0d837ac702e072184aa9c50dcb5f93932b65dc0aa98a0dd74192cd537cdb8f71/layer.tar
15b187765cd9c6f859ad241a63a6282c1118cdad40595674ce7a635965eb7acb.json
440b0b092f0d1d03518ae6602ccaac41e1c6edd3bc91d12aac7839ff06dd3557/
440b0b092f0d1d03518ae6602ccaac41e1c6edd3bc91d12aac7839ff06dd3557/VERSION
440b0b092f0d1d03518ae6602ccaac41e1c6edd3bc91d12aac7839ff06dd3557/json
440b0b092f0d1d03518ae6602ccaac41e1c6edd3bc91d12aac7839ff06dd3557/layer.tar
manifest.json
repositories

[tony@stapp01 ~]$ scp cluster_devops.tar banner@stapp03:~
cluster_devops.tar

[banner@stapp03 ~]$ ls -la
total 112228
drwx------ 1 banner banner      4096 Jun  3 20:49 .
drwxr-xr-x 1 root   root        4096 Sep 11  2023 ..
-rw-r--r-- 1 banner banner        18 Jun 20  2022 .bash_logout
-rw-r--r-- 1 banner banner       141 Jun 20  2022 .bash_profile
-rw-r--r-- 1 banner banner       376 Jun 20  2022 .bashrc
-rw------- 1 banner banner 114895360 Jun  3 20:49 cluster_devops.tar

[banner@stapp03 ~]$ docker image ls
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE

[banner@stapp03 ~]$ docker image load < cluster_devops.tar 
80098e3d304c: Loading layer [==================================================>]  78.74MB/78.74MB
4682e777bc0e: Loading layer [==================================================>]  36.14MB/36.14MB
Loaded image: cluster:devops

[banner@stapp03 ~]$ docker image ls
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
cluster             devops              ca690b564252        2 seconds ago       112MB
```
