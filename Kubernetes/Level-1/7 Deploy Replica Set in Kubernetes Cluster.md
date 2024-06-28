### Task
The Nautilus DevOps team is gearing up to deploy applications on a Kubernetes cluster for migration purposes. A team member has been tasked with creating a ReplicaSet outlined below:


1. Create a ReplicaSet using nginx image with latest tag (ensure to specify as nginx:latest) and name it nginx-replicaset.

2. Apply labels: app as nginx_app, type as front-end.

3. Name the container nginx-container. Ensure the replica count is 4.

Note: The kubectl utility on jump_host is set up to interact with the Kubernetes cluster.

### Solution
1. Create config file. nginx-replica.yaml
```
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: nginx-replicaset
  labels:
    app: nginx_app
    type: front-end
spec:
  replicas: 4
  selector:
    matchLabels:
      type: front-end
  template:
    metadata:
      labels:
        type: front-end
    spec:
      containers:
      - name: nginx-container
        image: nginx:latest                           
```
2. Apply file
```
thor@jumphost ~$ kubectl apply -f nginx-replica.yaml 
replicaset.apps/nginx-replicaset created

thor@jumphost ~$ kubectl get replicasets -o wide
NAME               DESIRED   CURRENT   READY   AGE   CONTAINERS        IMAGES         SELECTOR
nginx-replicaset   4         4         4       57s   nginx-container   nginx:latest   type=front-end

thor@jumphost ~$ kubectl get pods -o wide
NAME                     READY   STATUS    RESTARTS   AGE   IP           NODE                      NOMINATED NODE   READINESS GATES
nginx-replicaset-bt9cs   1/1     Running   0          32s   10.244.0.6   kodekloud-control-plane   <none>           <none>
nginx-replicaset-jmwhp   1/1     Running   0          32s   10.244.0.7   kodekloud-control-plane   <none>           <none>
nginx-replicaset-mdmgl   1/1     Running   0          32s   10.244.0.8   kodekloud-control-plane   <none>           <none>
nginx-replicaset-mjh7p   1/1     Running   0          32s   10.244.0.5   kodekloud-control-plane   <none>           <none>
```
