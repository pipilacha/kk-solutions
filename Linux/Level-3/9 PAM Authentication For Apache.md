### Task
We have a requirement where we want to password protect a directory in the Apache web server document root. We want to password protect http://<website-url>:<apache_port>/protected URL as per the following requirements (you can use any website-url for it like localhost since there are no such specific requirements as of now). Setup the same on App server 1 as per below mentioned requirements:


a. We want to use basic authentication.

b. We do not want to use htpasswd file based authentication. Instead, we want to use PAM authentication, i.e Basic Auth + PAM so that we can authenticate with a Linux user.
  - Install pam auth module. ```sudo yum install -y mod_authnz_pam```
    
  - In file ```/etc/httpd/conf.modules.d/55-authnz_pam.conf``` uncomment line:
    
    ```LoadModule authnz_pam_module modules/mod_authnz_pam.so```

c. We already have a user kareem with password B4zNgHA7Ya which you need to provide access to.
  - In file for directory block ```"/var/www/html"``` set:

    ```AllowOverride All```
  
  - Configure PAM
    ```
    # Create file in pam.d
    sudo vi /etc/pam.d/httpd-auth
    # Add basic config
    auth required pam_unix.so
    account required pam_unix.so    
    ```
  
  - Create file ```/var/www/html/protected/.htaccess``` and add the following directives
    ```
    AuthType Basic
    AuthName "Password Protected"
    AuthBasicProvider PAM
    AuthPAMService httpd-auth
    Require valid-user    
    ```

  - PAM gives the following issue:
    ```
    sudo journalctl -f
    Apr 04 22:15:51 stapp01.stratos.xfusioncorp.com unix_chkpwd[1114]: check pass; user unknown
    Apr 04 22:15:51 stapp01.stratos.xfusioncorp.com unix_chkpwd[1114]: password check failed for user (jim)
    Apr 04 22:15:51 stapp01.stratos.xfusioncorp.com httpd[1074]: pam_unix(httpd-auth:auth): authentication failure; logname= uid=48 euid=48 tty= ruser= rhost=172.16.238.3  user=jim
    ```
    User apache cannot read /etc/shadow. Let's create a facl granting it read permission.
    ```
    sudo setfacl -m u:apache:r /etc/shadow
    ```

d. You can test the same using a curl command from jump host curl http://<website-url>:<apache_port>/protected.

```
thor@jump_host ~$ curl --include stapp01:8080/protected/
HTTP/1.1 200 OK
Date: Thu, 04 Apr 2024 21:59:19 GMT
Server: Apache/2.4.6 (CentOS) PHP/7.2.26
Last-Modified: Thu, 04 Apr 2024 21:55:08 GMT
ETag: "26-6154c64857d8b"
Accept-Ranges: bytes
Content-Length: 38
Content-Type: text/html; charset=UTF-8

This is KodeKloud Protected Directory


thor@jump_host ~$ curl --include stapp01:8080/protected/
HTTP/1.1 401 Unauthorized
Date: Thu, 04 Apr 2024 22:00:46 GMT
Server: Apache/2.4.6 (CentOS) PHP/7.2.26
WWW-Authenticate: Basic realm="Password Protected"
Content-Length: 381
Content-Type: text/html; charset=iso-8859-1

<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
<html><head>
<title>401 Unauthorized</title>
</head><body>
<h1>Unauthorized</h1>
<p>This server could not verify that you
are authorized to access the document
requested.  Either you supplied the wrong
credentials (e.g., bad password), or your
browser doesn't understand how to supply
the credentials required.</p>
</body></html>


thor@jump_host ~$ curl --include --user jim:YchZHRcLkL stapp01:8080/protected/
HTTP/1.1 200 OK
Date: Thu, 04 Apr 2024 22:34:03 GMT
Server: Apache/2.4.6 (CentOS) PHP/7.2.26
Last-Modified: Thu, 04 Apr 2024 21:55:08 GMT
ETag: "26-6154c64857d8b"
Accept-Ranges: bytes
Content-Length: 38
Content-Type: text/html; charset=UTF-8

This is KodeKloud Protected Directory
```

Ref:
- https://techexpert.tips/apache/apache-pam-authentication/
- https://www.server-world.info/en/note?os=CentOS_8&p=httpd2&f=2
