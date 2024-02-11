# Task: 

There is a static website running in Stratos Datacenter. 
They have already configured the app servers and code is already deployed there. 
To make it work properly, they need to configure LBR server.
There are number of options for that, but team has decided to go with HAproxy. 
FYI, apache is running on port **6400 on all app servers**. Complete this task as per below details.


a. Install and configure HAproxy on LBR server using yum only and make sure all app servers are added to HAproxy load balancer. 
HAproxy must serve on default http port 
(Note: Please do not remove stats socket /var/lib/haproxy/stats entry from haproxy default config.).

b. Once done, you can access the website using StaticApp button on the top bar.

# Solution
1. Install haproxy

   > /usr/share/doc/haproxy/configuration.txt has detailed documentation
  
2. Edit default config file ```/etc/haproxy/haproxy.cfg```

   Modify frontend main block to bind server port
   ```
   #---------------------------------------------------------------------   
   # main frontend which proxys to the backends   
   #---------------------------------------------------------------------   
   frontend main
      bind *:80
      default_backend             app
   ```

   Modify backend app block to redirect traffic to Stratos Web Apps
   ```
   #---------------------------------------------------------------------
   # round robin balancing between the various backends
   #---------------------------------------------------------------------
   backend app
       balance     roundrobin
       server  app1 stapp01:3001 check
       server  app2 stapp02:3001 check
       server  app3 stapp03:3001 check
   ```
3. Enable and start haproxy, server apps should now be accesible.
