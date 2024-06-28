### Task
We have a backup management application UI hosted on Nautilus's backup server in Stratos DC. That backup management application code is deployed under Apache on the backup server itself, and Nginx is running as a reverse proxy on the same server. Apache and Nginx ports are 3003 and 8096, respectively. We have iptables firewall installed on this server. Make the appropriate changes to fulfill the requirements mentioned below:

We want to open all incoming connections to Nginx's port and block all incoming connections to Apache's port. Also make sure rules are permanent.

To persist we must save rules and apply them at boot. Package iptables-services saves us the hassle.

After installing package enable and start iptables (recently installed service).

Add the rules after starting service (adding them before starting server will delete them since there is no saved file).
```
sudo iptables -t filter -I INPUT -p tcp --dport 8096 -j ACCEPT
sudo iptables -t filter -I INPUT -p tcp --dport 3003 -j DROP
```

We can save current rules with:
```
sudo iptables-save > /etc/sysconfig/iptables
sudo systemctl restart iptables
```
or
```
sudo service iptables save
```

Ref:

https://www.cyberciti.biz/faq/how-to-save-iptables-firewall-rules-permanently-on-linux/

https://upcloud.com/resources/tutorials/configure-iptables-centos
