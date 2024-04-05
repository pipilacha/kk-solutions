### Task
Day by day traffic is increasing on one of the websites managed by the Nautilus production support team. Therefore, the team has observed a degradation in website performance. Following discussions about this issue, the team has decided to deploy this application on a high availability stack i.e on Nautilus infra in Stratos DC. They started the migration last month and it is almost done, as only the LBR server configuration is pending. Configure LBR server as per the information given below:


a. Install nginx on LBR (load balancer) server.

b. Configure load-balancing with the an http context making use of all App Servers.
```
# In file /etc/nginx/nginx.conf
# inside http block
# First configure pool of servers
upstream stratoswebservers {
    server stapp01:6400;
    server stapp02:6400;
    server stapp03:6400;
}

# In 'location /' block add:
proxy_pass http://stratoswebservers;
include proxy_params;

# Create file /etc/nginx/proxy_params and add the variables to pass
proxy_set_header Host $http_host;
proxy_set_header X-Real-IP $remote_addr;
proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
proxy_set_header X-Forwarded-Proto $scheme;
```


c. Make sure you do not update the apache port that is already defined in the apache configuration on all app servers, also make sure apache service is up and running on all app servers.
```
# App seems to be running on port 6400 on all webservers
thor@jump_host ~$ curl stapp01:6400
Welcome to xFusionCorp Industries!
thor@jump_host ~$ curl stapp02:6400
Welcome to xFusionCorp Industries!
thor@jump_host ~$ curl stapp03:6400
Welcome to xFusionCorp Industries!
```

d. Once done, you can access the website using StaticApp button on the top bar.
