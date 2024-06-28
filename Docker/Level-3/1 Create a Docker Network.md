### Task
The Nautilus DevOps team needs to set up several docker environments for different applications. One of the team members has been assigned a ticket where he has been asked to create some docker networks to be used later. Complete the task based on the following ticket description:

a. Create a docker network named as blog on App Server 2 in Stratos DC.

b. Configure it to use bridge drivers.

c. Set it to use subnet 192.168.0.0/24 and iprange 192.168.0.2/24.

### Solution
```
[steve@stapp02 ~]$ docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
82754be97464        bridge              bridge              local
477a2c769264        host                host                local
c50b9f77aff1        none                null                local


[steve@stapp02 ~]$ docker network create --driver bridge --subnet 192.168.0.0/24 --ip-range 192.168.0.2/24 blog
b56ea0a5ef1b0de9925bb7c2bbf6a94bf776d1b382be11e0f7388d2b828dcb1d

[steve@stapp02 ~]$ docker network inspect blog
[
    {
        "Name": "blog",
        "Id": "b56ea0a5ef1b0de9925bb7c2bbf6a94bf776d1b382be11e0f7388d2b828dcb1d",
        "Created": "2024-06-03T19:38:20.522749511Z",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "192.168.0.0/24",
                    "IPRange": "192.168.0.2/24"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {},
        "Options": {},
        "Labels": {}
    }
]
```
