### Task
The Nautilus DevOps team has noticed performance issues in some Kubernetes-hosted applications due to resource constraints. To address this, they plan to set limits on resource utilization. Here are the details:

Create a pod named httpd-pod with a container named httpd-container. Use the httpd image with the latest tag (specify as httpd:latest). Set the following resource limits:

Requests: Memory: 15Mi, CPU: 100m

Limits: Memory: 20Mi, CPU: 100m

Note: The kubectl utility on jump_host is configured to operate with the 

### Solution
1. Create pod according to spec:
```
apiVersion: v1
kind: Pod
metadata:
  name: httpd-pod
spec:
  containers:
  - name: httpd-container
    image: httpd:latest
    resources:
      requests:
        memory: "15Mi"
        cpu: "100m"
      limits:
        memory: "20Mi"
        cpu: "100m"
```
2. Apply configuration:
```
thor@jumphost ~$ kubectl apply -f httpd-pod.yaml 
pod/httpd-pod created
```
3. Pod's detailed info:
```
thor@jumphost ~$ kubectl describe pod httpd-pod
Name:             httpd-pod
Namespace:        default
Priority:         0
Service Account:  default
Node:             kodekloud-control-plane/172.17.0.2
Start Time:       Fri, 28 Jun 2024 19:53:21 +0000
Labels:           <none>
Annotations:      <none>
Status:           Running
IP:               10.244.0.5
IPs:
  IP:  10.244.0.5
Containers:
  httpd-container:
    Container ID:   containerd://5b2e92d709aae946e84034a1f260a4a7bf7efcb8f58bdaa2345992119add12f3
    Image:          httpd:latest
    Image ID:       docker.io/library/httpd@sha256:10182d88d7fbc5161ae0f6f758cba7adc56d4aae2dc950e51d72c0cf68967cea
    Port:           <none>
    Host Port:      <none>
    State:          Running
      Started:      Fri, 28 Jun 2024 19:53:30 +0000
    Ready:          True
    Restart Count:  0
    Limits:
      cpu:     100m
      memory:  20Mi
    Requests:
      cpu:        100m
      memory:     15Mi
....
```
