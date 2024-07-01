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
1. Approach 1 is to use ```kubectl edit -o yaml deployment nginx-deployment``` and ```kubectl edit -o yaml service nginx-service``` , write changes and exit editor.

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
   3. Modify service:
      ```
      thor@jumphost ~$ kubectl edit -o yaml service nginx-service
      
      apiVersion: v1
      kind: Service
      metadata:
        annotations:
          kubectl.kubernetes.io/last-applied-configuration: |
            {"apiVersion":"v1","kind":"Service","metadata":{"annotations":{},"name":"nginx-service","namespace":"default"},"spec":{"ports":[{"nodePort":30008,"port":80,"targetPort":80}],"selector":{"app":"nginx-app"},"type":"NodePort"}}
        creationTimestamp: "2024-07-01T17:08:58Z"
        name: nginx-service
        namespace: default
        resourceVersion: "7153"
        uid: bd7d2dbe-98a9-4500-8980-c62e85c409e6
      spec:
        clusterIP: 10.96.65.3
        clusterIPs:
        - 10.96.65.3
        externalTrafficPolicy: Cluster
        internalTrafficPolicy: Cluster
        ipFamilies:
        - IPv4
        ipFamilyPolicy: SingleStack
        ports:
        - nodePort: 32165
          port: 80
          protocol: TCP
          targetPort: 80
        selector:
          app: nginx-app
        sessionAffinity: None
        type: NodePort
      status:
        loadBalancer: {}
      ```
      
