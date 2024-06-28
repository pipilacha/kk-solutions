### TaskThe Nautilus DevOps team is diving into Kubernetes for application management. One team member has a task to create a pod according to the details below:

1. Create a pod named pod-httpd using the httpd image with the latest tag. Ensure to specify the tag as httpd:latest.

2. Set the app label to httpd_app, and name the container as httpd-container.

Note: The kubectl utility on jump_host is configured to operate with the Kubernetes cluster.

### Solution
Tasks does not specifies a namespace so we will use default.

1. Running pods
```
thor@jumphost ~$ kubectl get pods
No resources found in default namespace
```

2. Create pod according to specifications above. We will create it imperative, with a file called nginx-pod.yaml
```
apiVersion: v1
kind: Pod
metadata:
  name: pod-httpd
  labels:
    app: httpd_app
spec:
  containers:
  - name: httpd-container
    image: httpd:latest
```

3. Apply the configuration:
```
thor@jumphost ~$ kubectl apply -f nginx-pod.yaml 
pod/pod-httpd created
```

4. Running pods:
  ```
  thor@jumphost ~$ kubectl get pods
  NAME        READY   STATUS    RESTARTS   AGE
  pod-httpd   1/1     Running   0          3m51s
  ```
  Pod detailed info:
  ```
  thor@jumphost ~$ kubectl describe pod pod-httpd
  Name:             pod-httpd
  Namespace:        default
  Priority:         0
  Service Account:  default
  Node:             kodekloud-control-plane/172.17.0.2
  Start Time:       Fri, 28 Jun 2024 17:46:09 +0000
  Labels:           app=httpd_app
  Annotations:      <none>
  Status:           Running
  IP:               10.244.0.5
  IPs:
    IP:  10.244.0.5
  Containers:
    httpd-container:
      Container ID:   containerd://4e4c1198d8e5bfdd88305c4967a94e1c2e39a82a653059e29ef42b3317c02c15
      Image:          httpd:latest
  ...
  ```
