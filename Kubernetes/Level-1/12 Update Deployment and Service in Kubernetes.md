### Task
An application deployed on the Kubernetes cluster requires an update with new features developed by the Nautilus application development team. The existing setup includes a deployment named nginx-deployment and a service named nginx-service. Below are the necessary changes to be implemented without deleting the deployment and service:

1.) Modify the service nodeport from 30008 to 32165

2.) Change the replicas count from 1 to 5

3.) Update the image from nginx:1.19 to nginx:latest

Note: The kubectl utility on jump_host is configured to operate with the Kubernetes cluster.

### Solution
```
thor@jumphost ~$ kubectl get deployments -o wide

NAME               READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS        IMAGES       SELECTOR
nginx-deployment   1/1     1            1           17m   nginx-container   nginx:1.19   app=nginx-app
```
1. Approach 1 (interactive) is to use ```kubectl edit -o yaml deployment nginx-deployment``` and ```kubectl edit -o yaml service nginx-service``` , write changes and exit editor.

2. Approach 2. Use different commands for each configuration.
   1. First let's modify the image:
      ```
      thor@jumphost ~$ kubectl set image deployment nginx-deployment nginx-container=nginx:latest
      deployment.apps/nginx-deployment image updated
      thor@jumphost ~$ kubectl get deployments -o wide
      NAME               READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS        IMAGES         SELECTOR
      nginx-deployment   1/1     1            1           18m   nginx-container   nginx:latest   app=nginx-app
      ```
   2. Increase replicas:
      ```
      thor@jumphost ~$ kubectl scale --replicas=5 deployment nginx-deployment
      deployment.apps/nginx-deployment scaled
      
      thor@jumphost ~$ kubectl get deployments -o wide
      
      NAME               READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS        IMAGES         SELECTOR
      nginx-deployment   5/5     5            5           20m   nginx-container   nginx:latest   app=nginx-app
      ```
   3. Modify service: we need to pass the "port" key as it is the merge key
      ```
      thor@jumphost ~$ kubectl patch service nginx-service -p '{"spec": {"ports": [{"nodePort": 32165, "port": 80}]}}'
      service/nginx-service patched

      thor@jumphost ~$ kubectl describe service nginx-service
      Name:                     nginx-service
      Namespace:                default
      Labels:                   <none>
      Annotations:              <none>
      Selector:                 app=nginx-app
      Type:                     NodePort
      IP Family Policy:         SingleStack
      IP Families:              IPv4
      IP:                       10.96.248.70
      IPs:                      10.96.248.70
      Port:                     <unset>  80/TCP
      TargetPort:               80/TCP
      NodePort:                 <unset>  32165/TCP
      Endpoints:                10.244.0.10:80,10.244.0.6:80,10.244.0.7:80 + 2 more...
      Session Affinity:         None
      External Traffic Policy:  Cluster
      Events:                   <none>
      ```
      
