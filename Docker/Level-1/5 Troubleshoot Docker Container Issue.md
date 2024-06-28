### Task
An issue has arisen with a static website running in a container named nautilus on App Server 1. To resolve the issue, investigate the following details:


    1. Check if the container's volume /usr/local/apache2/htdocs is correctly mapped with the host's volume /var/www/html.

    2. Verify that the website is accessible on host port 8080 on App Server 1. Confirm that the command curl http://localhost:8080/ works on App Server 1.


### Solution
```
[tony@stapp01 ~]$ curl http://localhost:8080/
curl: (7) Failed to connect to localhost port 8080: Connection refused

[tony@stapp01 ~]$ docker ps -a
CONTAINER ID        IMAGE               COMMAND              CREATED             STATUS                     PORTS               NAMES
11fc11d2a550        httpd               "httpd-foreground"   2 minutes ago       Exited (0) 2 minutes ago                       nautilus

# Bind mount is mapped correctly
[tony@stapp01 ~]$ docker inspect nautilus
"Binds": [
                "/var/www/html:/usr/local/apache2/htdocs/"
            ],
# Port binding is fine, 8080 on host
"PortBindings": {
                "80/tcp": [
                    {
                        "HostIp": "",
                        "HostPort": "8080"
                    }
                ]
            },


[tony@stapp01 ~]$ docker start nautilus
nautilus
[tony@stapp01 ~]$ docker ps -a
CONTAINER ID        IMAGE               COMMAND              CREATED             STATUS              PORTS                  NAMES
11fc11d2a550        httpd               "httpd-foreground"   7 minutes ago       Up 12 seconds       0.0.0.0:8080->80/tcp   nautilus
[tony@stapp01 ~]$ curl http://localhost:8080/
Welcome to xFusionCorp Industries!
```
