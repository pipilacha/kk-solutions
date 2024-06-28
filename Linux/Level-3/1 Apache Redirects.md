### Task
The Nautilus devops team got some requirements related to some Apache config changes. They need to setup some redirects for some URLs. There might be some more changes need to be done. Below you can find more details regarding that:


1. httpd is already installed on app server 2. Configure Apache to listen on port 6200.

   ``` Listen 6200 ```

2. Configure Apache to add some redirects as mentioned below:

   We will use mod_rewrite. Turn on rewrite engine: ```RewriteEngine on```

    a.) Redirect ```http://stapp02.stratos.xfusioncorp.com:<Port>/``` to ```http://www.stapp02.stratos.xfusioncorp.com:<Port>/``` i.e non www to www. This must be a permanent redirect i.e 301

   Add rule:
   ```
   RewriteCond "%{HTTP_HOST}"   "!^www\.stapp02\.stratos\.xfusioncorp\.com" [NC]
   RewriteCond "%{HTTP_HOST}"   "!^$"
   RewriteCond "%{SERVER_PORT}" "^6200$"
   RewriteRule "^/?(.*)"        "http://www.stapp02.stratos.xfusioncorp.com:%{SERVER_PORT}/$1" [L,R=301,NE]
   ```

    b.) Redirect ```http://www.stapp02.stratos.xfusioncorp.com:<Port>/blog/``` to ```http://www.stapp02.stratos.xfusioncorp.com:<Port>/news/```. This must be a temporary redirect i.e 302.

   Add rule:
   ```
   RewriteCond "%{HTTP_HOST}"   "^www\.stapp02\.stratos\.xfusioncorp\.com" [NC]
   RewriteCond "%{SERVER_PORT}" "^6200$"
   RewriteRule "^/blog"        "http://www.stapp02.stratos.xfusioncorp.com:%{SERVER_PORT}/news/" [L,R=302,NE]
   ```

Ref:
- https://httpd.apache.org/docs/2.4/rewrite/remapping.html
- https://www.digitalocean.com/community/tutorials/how-to-rewrite-urls-with-mod_rewrite-for-apache-on-ubuntu-20-04#example-2-adding-conditions-with-logic-using-rewriteconds
