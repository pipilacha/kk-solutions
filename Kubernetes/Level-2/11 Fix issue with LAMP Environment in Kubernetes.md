### Task
One of the DevOps team member was trying to install a WordPress website on a LAMP stack which is essentially deployed on Kubernetes cluster. It was working well and we could see the installation page a few hours ago. However something is messed up with the stack now due to a website went down. Please look into the issue and fix it:


FYI, deployment name is lamp-wp and its using a service named lamp-service. The Apache is using http default port and nodeport is 30008. From the application logs it has been identified that application is facing some issues while connecting to the database in addition to other issues. Additionally, there are some environment variables associated with the pods like MYSQL_ROOT_PASSWORD, MYSQL_DATABASE,  MYSQL_USER, MYSQL_PASSWORD, MYSQL_HOST.

Also do not try to delete/modify any other existing components like deployment name, service name, types, labels etc.

Note: The kubectl utility on jump_host has been configured to work with the kubernetes cluster.

### solution
```
thor@jumphost ~$ kubectl get deployments -o wide
NAME      READY   UP-TO-DATE   AVAILABLE   AGE    CONTAINERS                            IMAGES                                         SELECTOR
lamp-wp   1/1     1            1           5m4s   httpd-php-container,mysql-container   webdevops/php-apache:alpine-3-php7,mysql:5.6   app=lamp,tier=frontend

# lets describe deployment

thor@jumphost ~$ kubectl describe deployments
Name:               lamp-wp
Namespace:          default
CreationTimestamp:  Wed, 03 Jul 2024 21:26:04 +0000
Labels:             app=lamp
Annotations:        deployment.kubernetes.io/revision: 1
Selector:           app=lamp,tier=frontend
Replicas:           1 desired | 1 updated | 1 total | 1 available | 0 unavailable
StrategyType:       Recreate
MinReadySeconds:    0
Pod Template:
  Labels:  app=lamp
           tier=frontend
  Containers:
   httpd-php-container:
    Image:      webdevops/php-apache:alpine-3-php7
    Port:       80/TCP
    Host Port:  0/TCP
    Environment:
      MYSQL_ROOT_PASSWORD:  <set to the key 'password' in secret 'mysql-root-pass'>  Optional: false
      MYSQL_DATABASE:       <set to the key 'database' in secret 'mysql-db-url'>     Optional: false
      MYSQL_USER:           <set to the key 'username' in secret 'mysql-user-pass'>  Optional: false
      MYSQL_PASSWORD:       <set to the key 'password' in secret 'mysql-user-pass'>  Optional: false
      MYSQL_HOST:           <set to the key 'host' in secret 'mysql-host'>           Optional: false
    Mounts:
      /opt/docker/etc/php/php.ini from php-config-volume (rw,path="php.ini")
   mysql-container:
    Image:      mysql:5.6
    Port:       3306/TCP
    Host Port:  0/TCP
    Environment:
      MYSQL_ROOT_PASSWORD:  <set to the key 'password' in secret 'mysql-root-pass'>  Optional: false
      MYSQL_DATABASE:       <set to the key 'database' in secret 'mysql-db-url'>     Optional: false
      MYSQL_USER:           <set to the key 'username' in secret 'mysql-user-pass'>  Optional: false
      MYSQL_PASSWORD:       <set to the key 'password' in secret 'mysql-user-pass'>  Optional: false
      MYSQL_HOST:           <set to the key 'host' in secret 'mysql-host'>           Optional: false
    Mounts:                 <none>
  Volumes:
   php-config-volume:
    Type:          ConfigMap (a volume populated by a ConfigMap)
    Name:          php-config
    Optional:      false
  Node-Selectors:  <none>
  Tolerations:     <none>
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Available      True    MinimumReplicasAvailable
  Progressing    True    NewReplicaSetAvailable
OldReplicaSets:  <none>
NewReplicaSet:   lamp-wp-56c7c454fc (1/1 replicas created)
Events:
  Type    Reason             Age   From                   Message
  ----    ------             ----  ----                   -------
  Normal  ScalingReplicaSet  7m8s  deployment-controller  Scaled up replica set lamp-wp-56c7c454fc to 1

# httpd is in fact using default container
# and env variables are pased by secret

# lets check service

thor@jumphost ~$ kubectl get services
NAME            TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
kubernetes      ClusterIP   10.96.0.1       <none>        4kube43/TCP          37m
lamp-service    NodePort    10.96.27.84     <none>        8080:30008/TCP   10m
mysql-service   ClusterIP   10.96.106.100   <none>        3306/TCP         10m

thor@jumphost ~$ kubectl describe service lamp-service 
Name:                     lamp-service
Namespace:                default
Labels:                   app=lamp
Annotations:              <none>
Selector:                 app=lamp,tier=frontend
Type:                     NodePort
IP Family Policy:         SingleStack
IP Families:              IPv4
IP:                       10.96.27.84
IPs:                      10.96.27.84
Port:                     <unset>  8080/TCP
TargetPort:               8080/TCP
NodePort:                 <unset>  30008/TCP
Endpoints:                10.244.0.5:8080
Session Affinity:         None
External Traffic Policy:  Cluster
Events:                   <none>

# tragetPort and Port are not the same as the one used by the container
# lets patch it using json patch

thor@jumphost ~$ kubectl patch service lamp-service --type='json' -p='[{"op": "replace", "path": "/spec/ports/0/port", "value": 80 }, {"op": "replace", "path": "/spec/ports/0/targetPort", "value": 80 }]'
service/lamp-service patched

# now we can access pod but it displays "Unable to Connect to ''" message
# lets check the lamp container logs

thor@jumphost ~$ kubectl logs lamp-wp-56c7c454fc-jlvxn -c httpd-php-container
-> Executing /opt/docker/provision/entrypoint.d/05-permissions.sh
-> Executing /opt/docker/provision/entrypoint.d/20-php-fpm.sh
-> Executing /opt/docker/provision/entrypoint.d/20-php.sh
-> Executing /opt/docker/bin/service.d/supervisor.d//10-init.sh
2024-07-03 22:26:32,248 CRIT Set uid to user 0
2024-07-03 22:26:32,248 WARN Included extra file "/opt/docker/etc/supervisor.d/apache.conf" during parsing
2024-07-03 22:26:32,248 WARN Included extra file "/opt/docker/etc/supervisor.d/cron.conf" during parsing
2024-07-03 22:26:32,248 WARN Included extra file "/opt/docker/etc/supervisor.d/dnsmasq.conf" during parsing
2024-07-03 22:26:32,248 WARN Included extra file "/opt/docker/etc/supervisor.d/php-fpm.conf" during parsing
2024-07-03 22:26:32,248 WARN Included extra file "/opt/docker/etc/supervisor.d/postfix.conf" during parsing
2024-07-03 22:26:32,248 WARN Included extra file "/opt/docker/etc/supervisor.d/ssh.conf" during parsing
2024-07-03 22:26:32,248 WARN Included extra file "/opt/docker/etc/supervisor.d/syslog.conf" during parsing
2024-07-03 22:26:32,291 INFO RPC interface 'supervisor' initialized
2024-07-03 22:26:32,291 INFO supervisord started with pid 1
2024-07-03 22:26:33,294 INFO spawned: 'syslogd' with pid 89
2024-07-03 22:26:33,297 INFO spawned: 'php-fpmd' with pid 90
2024-07-03 22:26:33,299 INFO spawned: 'apached' with pid 91
2024-07-03 22:26:33,389 INFO spawned: 'crond' with pid 94
-> Executing /opt/docker/bin/service.d/syslog-ng.d//10-init.sh
2024-07-03 22:26:33,390 INFO success: php-fpmd entered RUNNING state, process has stayed up for > than 0 seconds (startsecs)
2024-07-03 22:26:33,390 INFO success: apached entered RUNNING state, process has stayed up for > than 0 seconds (startsecs)
2024-07-03 22:26:33,390 INFO success: crond entered RUNNING state, process has stayed up for > than 0 seconds (startsecs)
-> Executing /opt/docker/bin/service.d/php-fpm.d//10-init.sh
Setting php-fpm user to application
[SYSLOG] syslog-ng[89]: syslog-ng starting up; version='3.7.2'
-> Executing /opt/docker/bin/service.d/httpd.d//10-init.sh
-> Executing /opt/docker/bin/service.d/cron.d//10-init.sh
[Wed Jul 03 22:26:33.688536 2024] [so:warn] [pid 115:tid 140219313638216] AH01574: module socache_shmcb_module is already loaded, skipping
[Wed Jul 03 22:26:33.694657 2024] [so:warn] [pid 115:tid 140219313638216] AH01574: module socache_shmcb_module is already loaded, skipping
[Wed Jul 03 22:26:33.789943 2024] [lbmethod_heartbeat:notice] [pid 115:tid 140219313638216] AH02282: No slotmem from mod_heartmonitor
[Wed Jul 03 22:26:33.792780 2024] [mpm_event:notice] [pid 115:tid 140219313638216] AH00489: Apache/2.4.25 (Unix) LibreSSL/2.4.4 configured -- resuming normal operations
[Wed Jul 03 22:26:33.792800 2024] [core:notice] [pid 115:tid 140219313638216] AH00094: Command line: '/usr/sbin/httpd -D FOREGROUND'
[03-Jul-2024 22:26:34] NOTICE: fpm is running, pid 90
[03-Jul-2024 22:26:34] NOTICE: ready to handle connections
2024-07-03 22:26:35,104 INFO success: syslogd entered RUNNING state, process has stayed up for > than 1 seconds (startsecs)
[03-Jul-2024 22:51:21] WARNING: [pool www] child 220 said into stderr: "NOTICE: PHP message: PHP Notice:  Undefined index: MYSQL_PASSWORDS in /app/index.php on line 4"
[03-Jul-2024 22:51:21] WARNING: [pool www] child 220 said into stderr: "NOTICE: PHP message: PHP Notice:  Undefined index: HOST_MYQSL in /app/index.php on line 5"
[php-fpm:access] 127.0.0.1 -  03/Jul/2024:22:51:21 +0000 "GET /index.php" 200 /app/index.php 10.174 2048 0.00%
[03-Jul-2024 22:51:21] WARNING: [pool www] child 220 said into stderr: "NOTICE: PHP message: PHP Warning:  mysqli_connect(): (HY000/2005): Unknown MySQL server host 'mysql' (-2) in /app/index.php on line 8"
[Wed Jul 03 22:51:21.986946 2024] [proxy_fcgi:error] [pid 117:tid 140219311970992] [client 10.244.0.1:13050] AH01071: Got error 'PHP message: PHP Notice:  Undefined index: MYSQL_PASSWORDS in /app/index.php on line 4\nPHP message: PHP Notice:  Undefined index: HOST_MYQSL in /app/index.php on line 5\nPHP message: PHP Warning:  mysqli_connect(): (HY000/2005): Unknown MySQL server host 'mysql' (-2) in /app/index.php on line 8\n', referer: https://0a69fd01741a4949.labs.kodekloud.com/
10.244.0.1 - - [03/Jul/2024:22:51:21 +0000] "GET / HTTP/1.1" 200 23 "https://0a69fd01741a4949.labs.kodekloud.com/" "Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:127.0) Gecko/20100101 Firefox/127.0"
[httpd:access] 30008-port-0a69fd01741a4949.labs.kodekloud.com:80 10.244.0.1 - - [03/Jul/2024:22:51:21 +0000] "GET / HTTP/1.1" 200 bytesIn:3469 bytesOut:207 reqTime:0
[03-Jul-2024 22:51:29] WARNING: [pool www] child 221 said into stderr: "NOTICE: PHP message: PHP Notice:  Undefined index: MYSQL_PASSWORDS in /app/index.php on line 4"
[03-Jul-2024 22:51:29] WARNING: [pool www] child 221 said into stderr: "NOTICE: PHP message: PHP Notice:  Undefined index: HOST_MYQSL in /app/index.php on line 5"
[php-fpm:access] 127.0.0.1 -  03/Jul/2024:22:51:29 +0000 "GET /index.php" 200 /app/index.php 2.580 2048 0.00%
[03-Jul-2024 22:51:29] WARNING: [pool www] child 221 said into stderr: "NOTICE: PHP message: PHP Warning:  mysqli_connect(): (HY000/2005): Unknown MySQL server host 'mysql' (-2) in /app/index.php on line 8"
[Wed Jul 03 22:51:29.845167 2024] [proxy_fcgi:error] [pid 120:tid 140219312253616] [client 10.244.0.1:4251] AH01071: Got error 'PHP message: PHP Notice:  Undefined index: MYSQL_PASSWORDS in /app/index.php on line 4\nPHP message: PHP Notice:  Undefined index: HOST_MYQSL in /app/index.php on line 5\nPHP message: PHP Warning:  mysqli_connect(): (HY000/2005): Unknown MySQL server host 'mysql' (-2) in /app/index.php on line 8\n', referer: https://0a69fd01741a4949.labs.kodekloud.com/
10.244.0.1 - - [03/Jul/2024:22:51:29 +0000] "GET / HTTP/1.1" 200 23 "https://0a69fd01741a4949.labs.kodekloud.com/" "Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:127.0) Gecko/20100101 Firefox/127.0"
[httpd:access] 30008-port-0a69fd01741a4949.labs.kodekloud.com:80 10.244.0.1 - - [03/Jul/2024:22:51:29 +0000] "GET / HTTP/1.1" 200 bytesIn:3468 bytesOut:207 reqTime:0
[03-Jul-2024 22:51:31] WARNING: [pool www] child 220 said into stderr: "NOTICE: PHP message: PHP Notice:  Undefined index: MYSQL_PASSWORDS in /app/index.php on line 4"
[03-Jul-2024 22:51:31] WARNING: [pool www] child 220 said into stderr: "NOTICE: PHP message: PHP Notice:  Undefined index: HOST_MYQSL in /app/index.php on line 5"
[03-Jul-2024 22:51:31] WARNING: [pool www] child 220 said into stderr: "NOTICE: PHP message: PHP Warning:  mysqli_connect(): (HY000/2005): Unknown MySQL server host 'mysql' (-2) in /app/index.php on line 8"
[php-fpm:access] 127.0.0.1 -  03/Jul/2024:22:51:31 +0000 "GET /index.php" 200 /app/index.php 1.737 2048 0.00%
[Wed Jul 03 22:51:31.348417 2024] [proxy_fcgi:error] [pid 117:tid 140219311782576] [client 10.244.0.1:54167] AH01071: Got error 'PHP message: PHP Notice:  Undefined index: MYSQL_PASSWORDS in /app/index.php on line 4\nPHP message: PHP Notice:  Undefined index: HOST_MYQSL in /app/index.php on line 5\nPHP message: PHP Warning:  mysqli_connect(): (HY000/2005): Unknown MySQL server host 'mysql' (-2) in /app/index.php on line 8\n', referer: https://0a69fd01741a4949.labs.kodekloud.com/
10.244.0.1 - - [03/Jul/2024:22:51:31 +0000] "GET / HTTP/1.1" 200 23 "https://0a69fd01741a4949.labs.kodekloud.com/" "Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:127.0) Gecko/20100101 Firefox/127.0"
[httpd:access] 30008-port-0a69fd01741a4949.labs.kodekloud.com:80 10.244.0.1 - - [03/Jul/2024:22:51:31 +0000] "GET / HTTP/1.1" 200 bytesIn:3469 bytesOut:207 reqTime:0

# ok.... variable names in deployment are different from ones in code, lets fix that

thor@jumphost ~$ kubectl patch deployment lamp-wp --type='json' -p='[{"op": "replace", "path": "/spec/template/spec/containers/0/env/3/name", "value": "MYSQL_PASSWORDS"}, {"op": "replace", "path": "/spec/template/spec/containers/0/env/4/name", "value": "HOST_MYQSL"}]'
deployment.apps/lamp-wp patched

```
![image](https://github.com/pipilacha/kk-solutions/assets/44210264/b311e7a4-a351-49cb-bb4f-8bdb7e8b4c63)




