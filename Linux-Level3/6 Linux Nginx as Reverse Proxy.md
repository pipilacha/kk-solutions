### Task
Nautilus system admin's team is planning to deploy a front end application for their backup utility on Nautilus Backup Server, so that they can manage the backups of different websites from a graphical user interface. They have shared requirements to set up the same; please accomplish the tasks as per detail given below:


a. Install Apache Server on Nautilus Backup Server and configure it to use 3004 port (do not bind it to 127.0.0.1 only, keep it default i.e let Apache listen on server's IP, hostname, localhost, 127.0.0.1 etc).
  ```
  # Edit file /etc/httpd/conf/httpd.conf
  Listen 3004
  ```
b. Install Nginx webserver on Nautilus Backup Server and configure it to use 8095.
  ```
  # In file /etc/nginx/nginx.conf
  # inside server block
  listen 8095 # change port
  ```

c. Configure Nginx as a reverse proxy server for Apache.
  ```
  # In file /etc/nginx/nginx.conf
  # inside location block add:
  proxy_pass http://localhost:3004;
  include proxy_params;

  # Create file /etc/nginx/proxy_params and add the variables to pass
  proxy_set_header Host $http_host;
  proxy_set_header X-Real-IP $remote_addr;
  proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  proxy_set_header X-Forwarded-Proto $scheme;
  ```
  https://docs.nginx.com/nginx/admin-guide/web-server/reverse-proxy/#passing-request-headers
  
  https://stackoverflow.com/questions/42589781/django-nginx-emerg-open-etc-nginx-proxy-params-failed-2-no-such-file
  
  https://nginx.org/en/docs/http/ngx_http_core_module.html#variables

d. There is a sample index file /home/thor/index.html on Jump Host, copy that file to Apache's document root.
  ```
  # From jump host copy file to clint's home
  scp index.html clint@stbkp01:~
  # In backup server mv to document root
  sudo mv index.html /var/www/html/
  ```
e. Make sure to start Apache and Nginx services.

f. You can test final changes using curl command, e.g ```curl http://<backup server IP or Hostname>:8095```.
