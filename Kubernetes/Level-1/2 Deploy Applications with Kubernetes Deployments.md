### Task
The Nautilus DevOps team is delving into Kubernetes for app management. One team member needs to create a deployment following these details:

Create a deployment named httpd to deploy the application httpd using the image httpd:latest (ensure to specify the tag)

Note: The kubectl utility on jump_host is set up to interact with the Kubernetes cluster.

### Solution
Will use default namespace as none is specified

1. Current deployments:
```
thor@jumphost ~$ kubectl get deployments
No resources found in default namespace.
```

2. I will create httpd-deploy.yaml file with the specifications above:
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: httpd
spec:
  replicas: 1
  selector:
    matchLabels:
      app: httpd-pod
  template:
    metadata:
      labels:
        app: httpd-pod
    spec:
      containers:
      - name: httpd-container
        image: httpd:latest
```

3. Apply configuration:
```
thor@jumphost ~$ kubectl apply -f httpd-deploy.yaml 
deployment.apps/httpd created
```

4. Current deployments:
```
thor@jumphost ~$ kubectl get deployments
NAME    READY   UP-TO-DATE   AVAILABLE   AGE
httpd   1/1     1            1           34s
```
