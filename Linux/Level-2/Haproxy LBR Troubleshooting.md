### Task

xFusionCorp Industries has an application running on Nautlitus infrastructure in Stratos Datacenter. The monitoring tool recognised that there is an issue with the haproxy service on LBR server. That needs to fixed to make the application work properly.


Troubleshoot and fix the issue, and make sure haproxy service is running on Nautilus LBR server. Once fixed, make sure you are able to access the website using StaticApp button on the top bar.

### Solution
1. journalctl output
```
Apr 03 00:57:04 stlb01.stratos.xfusioncorp.com systemd[1]: haproxy.service: cgroup is empty
Apr 03 00:57:04 stlb01.stratos.xfusioncorp.com systemd[1]: haproxy.service failed.
Apr 03 00:57:04 stlb01.stratos.xfusioncorp.com systemd[1]: Unit haproxy.service entered failed state.
Apr 03 00:57:04 stlb01.stratos.xfusioncorp.com systemd[1]: haproxy.service changed running -> failed
Apr 03 00:57:04 stlb01.stratos.xfusioncorp.com systemd[1]: haproxy.service: main process exited, code=exited, status=1/FAILURE
Apr 03 00:57:04 stlb01.stratos.xfusioncorp.com systemd[1]: Child 469 belongs to haproxy.service
Apr 03 00:57:04 stlb01.stratos.xfusioncorp.com haproxy-systemd-wrapper[469]: haproxy-systemd-wrapper: exit, haproxy RC=1
Apr 03 00:57:04 stlb01.stratos.xfusioncorp.com haproxy-systemd-wrapper[469]: [ALERT] 093/005704 (470) : Fatal errors found in configuration.
Apr 03 00:57:04 stlb01.stratos.xfusioncorp.com haproxy-systemd-wrapper[469]: [ALERT] 093/005704 (470) : Error(s) found in configuration file : /etc/haproxy/haproxy.cfg
Apr 03 00:57:04 stlb01.stratos.xfusioncorp.com haproxy-systemd-wrapper[469]: [ALERT] 093/005704 (470) : parsing [/etc/haproxy/haproxy.cfg:57] : balance only supports 'roundrobin', 'static-rr', 'leastconn', 'source', 'uri', 'url_param', 'hdr(name)' and 'rdp-cookie(name)' options.
Apr 03 00:57:04 stlb01.stratos.xfusioncorp.com haproxy-systemd-wrapper[469]: [ALERT] 093/005704 (470) : parsing [/etc/haproxy/haproxy.cfg:50] : balance only supports 'roundrobin', 'static-rr', 'leastconn', 'source', 'uri', 'url_param', 'hdr(name)' and 'rdp-cookie(name)' options.
Apr 03 00:57:04 stlb01.stratos.xfusioncorp.com haproxy-systemd-wrapper[469]: haproxy-systemd-wrapper: executing /usr/sbin/haproxy -f /etc/haproxy/haproxy.cfg -p /run/haproxy.pid -Ds
```
2. Current configuration file has 'round' set for balance, it is not a valid value. Modify to 'roundrobin'
```
46 #---------------------------------------------------------------------
47 # static backend for serving up images, stylesheets and such
48 #---------------------------------------------------------------------
49 backend static
50     balance     roundrobin
51     server      static 127.0.0.1:4331 check
52 
53 #---------------------------------------------------------------------
54 # round robin balancing between the various backends
55 #---------------------------------------------------------------------
56 backend app
57     balance     roundrobin
58     server  app1 stapp01:8086 check
59     server  app2 stapp02:8086 check
60     server  app3 stapp03:8086 check
```
3. Start haproxy service
