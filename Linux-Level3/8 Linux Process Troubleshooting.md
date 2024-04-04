### Task
The production support team of xFusionCorp Industries has deployed some of the latest monitoring tools to keep an eye on every service, application, etc. running on the systems. One of the monitoring systems reported about Apache service unavailability on one of the app servers in Stratos DC.


Identify the faulty app host and fix the issue. Make sure Apache service is up and running on all app hosts. They might not hosted any code yet on these servers so you need not to worry about if Apache isn't serving any pages or not, just make sure service is up and running. Also, make sure Apache is running on port 8086 on all app servers.

### Solution
1. curl from jump host to each app server.
   ```
   thor@jump_host ~$ curl stapp01:8086
   curl: (7) Failed to connect to stapp01 port 8086: Connection refused
   ```
  Only stapp01 returns error. Other two return default apache page. We have identified faulty server.

2. Let's check status of httpd service
  ```
    ● httpd.service - The Apache HTTP Server
       Loaded: loaded (/usr/lib/systemd/system/httpd.service; disabled; vendor preset: disabled)
       Active: inactive (dead)
         Docs: man:httpd(8)
               man:apachectl(8)
  ```
3. Let's check config file to which port it has configured
  ```
    Listen 8086
  ```
4. Port is fine. Let check if it is being used by another process
  ```
    [tony@stapp01 ~]$ sudo netstat -l4np
    Active Internet connections (only servers)
    Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
    tcp        0      0 127.0.0.11:41855        0.0.0.0:*               LISTEN      -                   
    tcp        0      0 0.0.0.0:111             0.0.0.0:*               LISTEN      1/init              
    tcp        0      0 127.0.0.1:8086          0.0.0.0:*               LISTEN      655/sendmail: accep 
    tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      526/sshd            
    udp        0      0 127.0.0.11:35884        0.0.0.0:*                           -                   
    udp        0      0 0.0.0.0:111             0.0.0.0:*                           1/init              
    udp        0      0 0.0.0.0:670             0.0.0.0:*                           507/rpcbind         
  ```
5. Let's disable sendmail and mask it
   ```
   sudo systemctl disable --now sendmail
   sudo systemctl mask sendmail
   ```
6. Start httpd
   
