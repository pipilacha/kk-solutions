# Task
  To secure our Nautilus infrastructure in Stratos Datacenter we have decided to install and configure firewalld on all app servers. 
  We have Apache and Nginx services running on these apps. Nginx is running as a reverse proxy server for Apache. 
  We might have more robust firewall settings in the future, but for now we have decided to go with the given requirements listed below:

  a. Allow all incoming connections on Nginx port, i.e 80.

  b. Block all incoming connections on Apache port, i.e 8080.

  c. All rules must be permanent.

  d. Zone should be public.

  e. If Apache or Nginx services aren't running already, please make sure to start them.

# Solution
1. Install firewalld
   > will use firewall-cmd to make changes to firewalld
   
3. Start firewalld, nginx and apache
   ```
   sudo systemctl enable --now firewalld && sudo systemctl start httpd && sudo systemctl start nginx
   sudo firewall-cmd --state -> will return the status of wirewalld
   ```
   
4. Set/Verify targets for each zone
   * ACCEPT for public and REJECT for block
   * ```sudo firewall-cmd --permanent --zone public --get-target```
   * ```sudo firewall-cmd --permanent --zone public --set-target ACCEPT```

5. Add port to each zone
   ```
   sudo firewall-cmd --zone public --add-port 80/tcp
   sudo firewall-cmd --zone block --add-port 8080/tcp
   ```
6. Persist runtime changes and reload firewalld
   ```
   sudo firewall-cmd --runtime-to-permanent
   sudo firewall-cmd --reload
   ```
7. We can check our zones info if we want to verify
   ```
   sudo firewall-cmd --permanent --info-zone public
   sudo firewall-cmd --permanent --info-zone block
   ```
   
