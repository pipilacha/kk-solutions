### Task
Our monitoring tool has reported an issue in Stratos Datacenter. One of our app servers has an issue, as its Apache service is not reachable on port 3004 (which is the Apache port). The service itself could be down, the firewall could be at fault, or something else could be causing the issue.


Use tools like telnet, netstat, etc. to find and fix the issue. Also make sure Apache is reachable from the jump host without compromising any security settings.

Once fixed, you can test the same using command curl http://stapp01:3004 command from jump host.

### Solution

1. Checking if service is running. Status for httpd service returns:
   ```
    ● httpd.service - The Apache HTTP Server
     Loaded: loaded (/usr/lib/systemd/system/httpd.service; disabled; vendor preset: disabled)
     Active: failed (Result: exit-code) since Wed 2024-04-03 21:33:30 UTC; 1min 41s ago
       Docs: man:httpd.service(8)
    Process: 669 ExecStart=/usr/sbin/httpd $OPTIONS -DFOREGROUND (code=exited, status=1/FAILURE)
   Main PID: 669 (code=exited, status=1/FAILURE)
     Status: "Reading configuration..."
  
    Apr 03 21:33:30 stapp01.stratos.xfusioncorp.com httpd[669]: (98)Address already in use: AH00072: make_sock:   could not bind to address 0.0.0.0:3004
    Apr 03 21:33:30 stapp01.stratos.xfusioncorp.com httpd[669]: no listening sockets available, shutting down
    Apr 03 21:33:30 stapp01.stratos.xfusioncorp.com httpd[669]: AH00015: Unable to open logs
    Apr 03 21:33:30 stapp01.stratos.xfusioncorp.com systemd[1]: httpd.service: Child 669 belongs to httpd.service.
    Apr 03 21:33:30 stapp01.stratos.xfusioncorp.com systemd[1]: httpd.service: Main proc  ess exited, code=exited, status=1/FAILURE
    Apr 03 21:33:30 stapp01.stratos.xfusioncorp.com systemd[1]: httpd.service: Failed with result 'exit-code'.
    Apr 03 21:33:30 stapp01.stratos.xfusioncorp.com systemd[1]: httpd.service: Changed start -> failed
    Apr 03 21:33:30 stapp01.stratos.xfusioncorp.com systemd[1]: httpd.service: Job httpd.service/start finished, result=failed
    Apr 03 21:33:30 stapp01.stratos.xfusioncorp.com systemd[1]: Failed to start The Apache HTTP Server.
    Apr 03 21:33:30 stapp01.stratos.xfusioncorp.com systemd[1]: httpd.service: Unit entered failed state.
    ```
    Port is already in use

2. Check which ps is using port 3004. netstat returns:

  ```
    [tony@stapp01 ~]$ sudo netstat -4lnp
    Active Internet connections (only servers)
    Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
    tcp        0      0 127.0.0.11:35309        0.0.0.0:*               LISTEN      -                   
    tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      465/sshd            
    tcp        0      0 127.0.0.1:3004          0.0.0.0:*               LISTEN      608/sendmail: accep 
    udp        0      0 127.0.0.11:51420        0.0.0.0:*                           -        
  ```
3. Stop senmail service. It is not enabled so no need to disable, but lets mask it.
   ```
   sudo systemctl stop sendmail.service
   sudo systemctl mask sendmail.service
   ```
4. Restart httpd

   Server still unreachable.

5. Add an entry to iptables that allow traffic to that port:  ```sudo iptables -t filter -I INPUT -p tcp --dport 3004 -j ACCEPT```
  
