### Task
We have one of our websites up and running on our Nautilus infrastructure in Stratos DC. Our security team has raised a concern that right now Apache’s port i.e 8082 is open for all since there is no firewall installed on these hosts. So we have decided to add some security layer for these hosts and after discussions and recommendations we have come up with the following requirements:


1. Install iptables and all its dependencies on each app host.
   Let's install iptables-services also, easier to manage persistance.

3. Block incoming port 8082 on all apps for everyone except for LBR host.
   ```
   # Start service before writing rules
   sudo iptables -t filter -I INPUT -s stlb01 -p tcp --dport 8082 -j ACCEPT # Allow LBR to reach host
   sudo iptables -t filter -I INPUT 2 -p tcp --dport 8082 -j DROP # Block anything else to that port
   ```

5. Make sure the rules remain, even after system reboot.
  ```
  sudo service iptables save
  ```
