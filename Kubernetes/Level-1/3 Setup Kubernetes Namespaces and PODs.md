### Task
The Nautilus DevOps team is planning to deploy some micro services on Kubernetes platform. The team has already set up a Kubernetes cluster and now they want to set up some namespaces, deployments etc. Based on the current requirements, the team has shared some details as below:

Create a namespace named dev and deploy a POD within it. Name the pod dev-nginx-pod and use the nginx image with the latest tag. Ensure to specify the tag as nginx:latest.

Note: The kubectl utility on jump_host is configured to operate with the Kubernetes cluster.

### Solution
1. Current namespaces:
```
thor@jumphost ~$ kubectl get namespaces
NAME                 STATUS   AGE
default              Active   38m
kube-node-lease      Active   38m
kube-public          Active   38m
kube-system          Active   38m
local-path-storage   Active   38m
```
2. Created namespace ```dev```
```
thor@jumphost ~$ kubectl create namespace dev
namespace/dev created
```
3. New list of namespaces:
```
thor@jumphost ~$ kubectl get namespaces
NAME                 STATUS   AGE
default              Active   40m
dev                  Active   26s
kube-node-lease      Active   40m
kube-public          Active   40m
kube-system          Active   40m
local-path-storage   Active   39m
```
4. Create Pod according to specifications above. nginx-pod.yaml:
```
apiVersion: v1
kind: Pod
metadata:
  name: dev-nginx-pod
spec:
  containers:
  - name: nginx
    image: nginx:latest
```
5. Apply config to namespace ```dev```:
```
thor@jumphost ~$ kubectl -n dev apply -f nginx-pod.yaml 
pod/dev-nginx-pod created
```
6. Pods in namespace
```
thor@jumphost ~$ kubectl -n dev get pods
NAME            READY   STATUS    RESTARTS   AGE
dev-nginx-pod   1/1     Running   0          73s
```
