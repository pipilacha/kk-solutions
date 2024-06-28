### Task
One of the Nautilus DevOps team members was working to configure services on a kkloud container that is running on App Server 2 in Stratos Datacenter. Due to some personal work he is on PTO for the rest of the week, but we need to finish his pending work ASAP. Please complete the remaining work as per details given below:

a. Install apache2 in kkloud container using apt that is running on App Server 2 in Stratos Datacenter.

b. Configure Apache to listen on port 5002 instead of default http port. Do not bind it to listen on specific IP or hostname only, i.e it should listen on localhost, 127.0.0.1, container ip, etc.

c. Make sure Apache service is up and running inside the container. Keep the container in running state at the end.

### Solution
1. Attach to the conainer
```
[steve@stapp02 ~]$ docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED              STATUS              PORTS               NAMES
5f7743c2c198        ubuntu:18.04        "/bin/bash"         About a minute ago   Up About a minute                       kkloud

[steve@stapp02 ~]$ docker exec -ti kkloud bash
root@5f7743c2c198:/#
```
2. Install apache2
```
root@5f7743c2c198:/# apt install -y apache2

root@5f7743c2c198:/# service apache2 status
 * apache2 is not running
```
3. Configure apache to listen port 5002
```
root@5f7743c2c198:/# cat /etc/apache2/ports.conf   
# If you just change the port or add more ports here, you will likely also
# have to change the VirtualHost statement in
# /etc/apache2/sites-enabled/000-default.conf

Listen 80

<IfModule ssl_module>
        Listen 443
</IfModule>

<IfModule mod_gnutls.c>
        Listen 443
</IfModule>

# vim: syntax=apache ts=4 sw=4 sts=4 sr noet

root@5f7743c2c198:/# sed -i 's/80/5002/' /etc/apache2/ports.conf > /dev/null

root@5f7743c2c198:/# cat /etc/apache2/ports.conf 
# If you just change the port or add more ports here, you will likely also
# have to change the VirtualHost statement in
# /etc/apache2/sites-enabled/000-default.conf

Listen 5002

<IfModule ssl_module>
        Listen 443
</IfModule>

<IfModule mod_gnutls.c>
        Listen 443
</IfModule>

# vim: syntax=apache ts=4 sw=4 sts=4 sr noet
```
4. Start service
```
root@5f7743c2c198:/# service apache2 start
 * Starting Apache httpd web server apache2                                                                                             AH00558: apache2: Could not reliably determine the server's fully qualified domain name, using 172.18.0.2. Set the 'ServerName' directive globally to suppress this message
 * 
root@5f7743c2c198:/# service apache2 status
 * apache2 is running
```
5. Test apache server
```
root@5f7743c2c198:/# curl localhost:5002/

<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml">
  <!--
    Modified from the Debian original for Ubuntu
    Last updated: 2016-11-16
    See: https://launchpad.net/bugs/1288690
  -->
....
```
