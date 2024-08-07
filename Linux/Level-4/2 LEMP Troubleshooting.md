### Task
We have LEMP stack configured on apps and database server in Stratos DC. Its using Nginx + php-fpm, for now we have deployed a sample php page on these apps. Due to some misconfiguration the php page is not loading on the web server. Seems like at least two app servers are having issues. Find below more details and make sure website works on LBR URL and locally on each app as well.


1. Nginx is supposed to run on port 80 on all app servers.

2. Nginx document root is /var/www/html/

3. Test the webpage on LBR URL (use LBR button on the top bar) and locally on each app server to make sure it works. It must not display any error message or nginx default page.

### Solution
1. Let's indentify which servers have issues
   ```
   thor@jumphost ~$ curl stapp01:80
   curl: (7) Failed to connect to stapp01 port 80: Connection refused

   thor@jumphost ~$ curl stapp02:80
   <html>
   <head><title>404 Not Found</title></head>
   <body>
   <center><h1>404 Not Found</h1></center>
   <hr><center>nginx/1.20.1</center>
   </body>
   </html>

   thor@jumphost ~$ curl stapp03:80 | head -10
    % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                   Dload  Upload   Total   Spent    Left  Speed
    0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0<!DOCTYPE html>
   <html lang="en">
   <head>
     <meta name="generator" content="HTML Tidy for HTML5 for Linux version 5.7.28">
     <title>HTTP Server Test Page powered by CentOS</title>
     <meta charset="utf-8">
     <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
     <link rel="shortcut icon" href="http://www.centos.org/favicon.ico">
     <style type="text/css">
         /*<![CDATA[*/
    13  445k   13 63469    0     0  60.5M      0 --:--:-- --:--:-- --:--:-- 60.5M
   curl: (23) Failure writing output to destination
   ```
   - servers 1: rejecting connection
   - server 2: does not find site.
   - server 3: wrong site
3. Let's fix server 1:
```
[tony@stapp01 ~]$ sudo ss -l4tnp
State             Recv-Q             Send-Q                         Local Address:Port                          Peer Address:Port            Process                                                                                                                                      
LISTEN            0                  511                                  0.0.0.0:8084                               0.0.0.0:*                users:(("nginx",pid=1890,fd=6),("nginx",pid=1889,fd=6),("nginx",pid=1882,fd=6),("nginx",pid=1876,fd=6),("nginx",pid=1871,fd=6),("nginx",pid=1870,fd=6),("nginx",pid=1869,fd=6),("nginx",pid=1865,fd=6),("nginx",pid=1864,fd=6),("nginx",pid=1863,fd=6),("nginx",pid=1862,fd=6),("nginx",pid=1861,fd=6),("nginx",pid=1860,fd=6),("nginx",pid=1714,fd=6),("nginx",pid=1713,fd=6),("nginx",pid=1712,fd=6),("nginx",pid=1711,fd=6),("nginx",pid=1710,fd=6),("nginx",pid=1709,fd=6),("nginx",pid=1708,fd=6),("nginx",pid=1707,fd=6),("nginx",pid=1706,fd=6),("nginx",pid=1705,fd=6),("nginx",pid=1704,fd=6),("nginx",pid=1703,fd=6),("nginx",pid=1702,fd=6),("nginx",pid=1701,fd=6),("nginx",pid=1700,fd=6),("nginx",pid=1699,fd=6),("nginx",pid=1698,fd=6),("nginx",pid=1697,fd=6),("nginx",pid=1694,fd=6),("nginx",pid=1692,fd=6),("nginx",pid=1689,fd=6),("nginx",pid=1688,fd=6),("nginx",pid=1687,fd=6),("nginx",pid=1686,fd=6))
LISTEN            0                  128                                  0.0.0.0:22                                 0.0.0.0:*                users:(("sshd",pid=1154,fd=3))                                                                                                              
LISTEN            0                  4096                              127.0.0.11:36067                              0.0.0.0:*                                                                        
```
  - Nginx is using wrong port
```
[tony@stapp01 ~]$sudo sed '{s/8084/80/g}' /etc/nginx/nginx.conf | sudo tee /etc/nginx/nginx.conf.new > /dev/null
[tony@stapp01 ~]$ wc /etc/nginx/nginx.conf.*
 117  264 2656 /etc/nginx/nginx.conf.default
  94  253 2738 /etc/nginx/nginx.conf.new
  94  253 2742 /etc/nginx/nginx.conf.old
 305  770 8136 total
[tony@stapp01 ~]$ sudo mv /etc/nginx/nginx.conf.new /etc/nginx/nginx.conf
[tony@stapp01 ~]$ sudo systemctl reload-or-restart nginx.service

thor@jumphost ~$ curl stapp01:80
<html>
<head><title>403 Forbidden</title></head>
<body>
<center><h1>403 Forbidden</h1></center>
<hr><center>nginx/1.20.1</center>
</body>
</html>
```
  - Now it cannot access index
```
    server {
        listen       80 default_server;
        listen       [::]:80 default_server;
        server_name  _;
        root         /var/www/html/;
        index index.html index.htm;
```
  - Need to add `index.php` in index record, then reload/restart
```
thor@jumphost ~$ curl stapp01:80
App is able to connect to the database using user kodekloud_joy
```
4. Let's fix server 2:
```
server {
        listen       80 default_server;
        listen       [::]:80 default_server;
        server_name  _;
        root         /var/www/html/;
        index index.html index.htm index.php;

        location ~ .php$ {
           try_files $uri =404;
           fastcgi_pass unix:/var/opt/remi/php74/run/php-fpm/www;
           fastcgi_index index.php;
           fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
           include fastcgi_params;
        }
```
  - `fastcgi_pass record` should be ```www.sock```, restart
```
thor@jumphost ~$ curl stapp02:80
App is able to connect to the database using user kodekloud_joy
```
5. For server 3:
```
server {
        listen       80 default_server;
        listen       [::]:80 default_server;
        server_name  _;
        root         /usr/share/nginx/html/;
        index index.php index.html index.htm;
```
  - Change server root to `/var/www/html/`, then restart/reload
```
thor@jumphost ~$ curl stapp03:80
App is able to connect to the database using user kodekloud_joy
```
