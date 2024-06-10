### Task
The Nautilus Application development team recently finished development of one of the apps that they want to deploy on a containerized platform. The Nautilus Application development and DevOps teams met to discuss some of the basic pre-requisites and requirements to complete the deployment. The team wants to test the deployment on one of the app servers before going live and set up a complete containerized stack using a docker compose fie. Below are the details of the task:


    On App Server 1 in Stratos Datacenter create a docker compose file /opt/sysops/docker-compose.yml (should be named exactly).

    The compose should deploy two services (web and DB), and each service should deploy a container as per details below:

For web service:

a. Container name must be php_blog.

b. Use image php with any apache tag. Check here for more details.

c. Map php_blog container's port 80 with host port 6100

d. Map php_blog container's /var/www/html volume with host volume /var/www/html.

For DB service:

a. Container name must be mysql_blog.

b. Use image mariadb with any tag (preferably latest). Check here for more details.

c. Map mysql_blog container's port 3306 with host port 3306

d. Map mysql_blog container's /var/lib/mysql volume with host volume /var/lib/mysql.

e. Set MYSQL_DATABASE=database_blog and use any custom user ( except root ) with some complex password for DB connections.

    After running docker-compose up you can access the app with curl command curl <server-ip or hostname>:6100/

For more details check here.

Note: Once you click on FINISH button, all currently running/stopped containers will be destroyed and stack will be deployed again using your compose file.

### Solution
```
[tony@stapp01 ~]$ cd /opt/sysops/

[tony@stapp01 sysops]$ ls -la
total 8
drwxr-xr-x 2 root root 4096 Jun  9 23:42 .
drwxr-xr-x 1 root root 4096 Jun  9 23:42 ..

[tony@stapp01 sysops]$ sudo vi docker-compose.yml
# docker-compose.yml content
name: sysops

services:
  php_blog:
    container_name: php_blog
    image: php:apache-bullseye
    ports:
      - "6100:80"
    volumes:
      - /var/www/html:/var/www/html
  db:
    container_name: mysql_blog
    image: mariadb:latest
    ports:
      - "3306:3306"
    volumes:
      - /var/lib/mysql:/var/lib/mysql
    environment:
      - MYSQL_DATABASE=database_blog
      - MARIADB_USER=kkengineer
      - MARIADB_PASSWORD=kodekloud1
      - MARIADB_RANDOM_ROOT_PASSWORD=1

[tony@stapp01 sysops]$ docker ps -a
permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Get "http://%2Fvar%2Frun%2Fdocker.sock/v1.45/containers/json?all=1": dial unix /var/run/docker.sock: connect: permission denied

[tony@stapp01 sysops]$ sudo usermod -aG docker tony
[sudo] password for tony: 

[tony@stapp01 sysops]$ docker ps -a
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES

[tony@stapp01 sysops]$ docker ps -a
CONTAINER ID   IMAGE                 COMMAND                  CREATED              STATUS          PORTS                    NAMES
c3beca559f58   mariadb:latest        "docker-entrypoint.s…"   About a minute ago   Up 40 seconds   0.0.0.0:3306->3306/tcp   mysql_blog
f12968ffc30c   php:apache-bullseye   "docker-php-entrypoi…"   About a minute ago   Up 40 seconds   0.0.0.0:6100->80/tcp     php_blog

[tony@stapp01 sysops]$ curl localhost:6100
<html>
    <head>
        <title>Welcome to xFusionCorp Industries!</title>
    </head>

    <body>
        Welcome to xFusionCorp Industries!    </body>
</html>
```
### But ofc, the best solution is using secrets. 
https://docs.docker.com/compose/use-secrets/
````
[banner@stapp03 ~]$ cd /opt/security/

[banner@stapp03 security]$ sudo vi docker-compose.yml
# docker-compose.yml content
name: sec

services:
  php_host:
    container_name: php_host
    image: php:apache-bullseye
    ports:
      - "5001:80"
    volumes:
      - /var/www/html:/var/www/html
  db:
    container_name: mysql_host
    image: mariadb:latest
    ports:
      - "3306:3306"
    volumes:
      - /var/lib/mysql:/var/lib/mysql
    environment:
      - MYSQL_DATABASE=database_host
      - MARIADB_USER=kkengineer
      - MARIADB_PASSWORD=/run/secrets/db_password
      - MARIADB_ROOT_PASSWORD=/run/secrets/db_root_password
    secrets:
      - db_password
      - db_root_password

secrets:
  db_password:
    file: db_password.txt
  db_root_password:
    file: db_root_password.txt

[banner@stapp03 security]$ sudo su -
[root@stapp03 ~]# echo 'kodekloud1' > db_password.txt && echo 'kodekloudroot' > db_root_password.txt && mv db_password.txt db_root_password.txt /opt/security/
[root@stapp03 ~]# logout

[banner@stapp03 security]$ ls -la
total 20
drwxr-xr-x 2 root root 4096 Jun 10 00:54 .
drwxr-xr-x 1 root root 4096 Jun 10 00:43 ..
-rw-r--r-- 1 root root   11 Jun 10 00:47 db_password.txt
-rw-r--r-- 1 root root   14 Jun 10 00:47 db_root_password.txt
-rw-r--r-- 1 root root  678 Jun 10 00:45 docker-compose.yml

[banner@stapp03 security]$ docker compose up -d
[+] Running 23/23
 ✔ db Pulled                                                                                          93.0s 
   ✔ 00d679a470c4 Pull complete                                                                       14.1s 
   ✔ 5eddd2b094bc Pull complete                                                                       17.5s 
   ✔ c7bae458de01 Pull complete                                                                       21.2s 
   ✔ 60fd58cc8d1b Pull complete                                                                       24.4s 
   ✔ b44b62991c05 Pull complete                                                                       28.0s 
   ✔ f5dedb1c2846 Pull complete                                                                       51.6s 
   ✔ fa481306a939 Pull complete                                                                       70.7s 
   ✔ 689797659655 Pull complete                                                                       92.3s 
 ✔ php_host Pulled                                                                                   154.1s 
   ✔ 728328ac3bde Pull complete                                                                       16.8s 
   ✔ 50f6f7a35042 Pull complete                                                                       19.1s 
   ✔ 5f3967c95942 Pull complete                                                                       43.6s 
   ✔ 30a12ef0702b Pull complete                                                                       63.0s 
   ✔ 24887d144abc Pull complete                                                                       89.3s 
   ✔ 21d17ed3fb79 Pull complete                                                                      103.5s 
   ✔ 15aa7ea646d5 Pull complete                                                                      110.0s 
   ✔ e43999f821b5 Pull complete                                                                      116.8s 
   ✔ 3612853a5ac0 Pull complete                                                                      123.4s 
   ✔ 091dda311e32 Pull complete                                                                      132.0s 
   ✔ 79e46d2898f0 Pull complete                                                                      138.9s 
   ✔ 000477a942e1 Pull complete                                                                      146.3s 
   ✔ dddc8dfbc49f Pull complete                                                                      153.7s 
[+] Running 3/3
 ✔ Network sec_default   Created                                                                       0.2s 
 ✔ Container mysql_host  Started                                                                      47.7s 
 ✔ Container php_host    Started                                                                      47.6s 

[banner@stapp03 security]$ docker ps -a
CONTAINER ID   IMAGE                 COMMAND                  CREATED          STATUS          PORTS                    NAMES
05208870878b   mariadb:latest        "docker-entrypoint.s…"   59 seconds ago   Up 12 seconds   0.0.0.0:3306->3306/tcp   mysql_host
9be83984cad9   php:apache-bullseye   "docker-php-entrypoi…"   59 seconds ago   Up 12 seconds   0.0.0.0:5001->80/tcp     php_host

[banner@stapp03 security]$ curl localhost:5001
<html>
    <head>
        <title>Welcome to xFusionCorp Industries!</title>
    </head>

    <body>
        Welcome to xFusionCorp Industries!    </body>
</html>
```
