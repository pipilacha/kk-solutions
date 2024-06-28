### Task
The system admins team of xFusionCorp Industries needs to deploy a new application on App Server 2 in Stratos Datacenter. They have some pre-requites to get ready that server for application deployment. Prepare the server as per requirements shared below:


1. Install and configure nginx on App Server 2.

2. On App Server 2 there is a self signed SSL certificate and key present at location /tmp/nautilus.crt and /tmp/nautilus.key. Move them to some appropriate location and deploy the same in Nginx.
   ```
   sudo mkdir /etc/pki/nginx/
   sudo mkdir /etc/pki/nginx/private/
   sudo mv /tmp/nautilus.crt /etc/pki/nginx/
   sudo mv /tmp/nautilus.key /etc/pki/nginx/private/
   ```
   - Uncomment and modify 443 server configuration block to include the locations of the certificate and key
     ```
       # Settings for a TLS enabled server.
       server {
          listen       443 ssl http2 default_server;
          listen       [::]:443 ssl http2 default_server;
          server_name  _;
          root         /usr/share/nginx/html;
  
          ssl_certificate "/etc/pki/nginx/nautilus.crt";
          ssl_certificate_key "/etc/pki/nginx/private/nautilus.key";
          ssl_session_cache shared:SSL:1m;
          ssl_session_timeout  10m;
          ssl_ciphers PROFILE=SYSTEM;
          ssl_prefer_server_ciphers on;
  
          # Load configuration files for the default server block.
          include /etc/nginx/default.d/*.conf;
  
          location / {
          }
  
          error_page 404 /404.html;
              location = /40x.html {
          }
  
          error_page 500 502 503 504 /50x.html;
              location = /50x.html {
          }
      }
      ```

4. Create an index.html file with content Welcome! under Nginx document root.
   ```
   echo 'Welcome!' | sudo tee /usr/share/nginx/html/index.html
   ```

6. For final testing try to access the App Server 2 link (either hostname or IP) from jump host using curl command. For example ```curl -Ik https://<app-server-ip>/```.
   ```
   thor@jump_host ~$ curl --include --insecure https://stapp02/
   HTTP/2 200
   server: nginx/1.14.1
   date: Thu, 04 Apr 2024 20:46:16 GMT
   content-type: text/html
   content-length: 9
   last-modified: Thu, 04 Apr 2024 20:46:12 GMT
   etag: "660f1194-9"
   accept-ranges: bytes

   Welcome!
   ```
