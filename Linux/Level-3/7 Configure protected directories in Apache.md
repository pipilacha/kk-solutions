### Task
xFusionCorp Industries has hosted several static websites on Nautilus Application Servers in Stratos DC. There are some confidential directories in the document root that need to be password protected. Since they are using Apache for hosting the websites, the production support team has decided to use .htaccess with basic auth. There is a website that needs to be uploaded to /var/www/html/itadmin on Nautilus App Server 2. However, we need to set up the authentication before that.


1. Create /var/www/html/itadmin direcotry if doesn't exist.

2. Add a user ravi in htpasswd and set its password to GyQkFRVNr3.
   * Let's create user and password.
     ```
     # I will store in /var/htaccess
     sudo mkdir /var/htaccess
     sudo htpasswd -c /var/htaccess/passwords ravi
     ```
   * Configure .htaccess file
     ```
     sudo su -
     cat << EOF > /var/www/html/itadmin/.htaccess
     AuthType Basic
     AuthName "Password required"
     AuthUserFile "/var/htaccess/passwords"
     Require user ravi
     EOF
     ```
4. There is a file /tmp/index.html present on Jump Server. Copy the same to the directory you created, please make sure default document root should remain /var/www/html. Also website should work on URL ```http://<app-server-hostname>:8080/itadmin/```
```
# From jump host
scp /tmp/index.html steve@stapp02:~
# From stapp02
sudo mv index.html /var/www/html/itadmin
sudo chown root:root /var/www/html/itadmin/index.html
```
Restart/start httpd.

```
thor@jump_host ~$ curl http://stapp02:8080/itadmin/
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
thor@jump_host ~$ curl --user ravi:GyQkFRVNr3 http://stapp02:8080/itadmin/
This is xFusionCorp Industries Protected Directory!
```

Ref:
* https://httpd.apache.org/docs/2.4/howto/htaccess.html
* https://httpd.apache.org/docs/2.4/howto/auth.html
