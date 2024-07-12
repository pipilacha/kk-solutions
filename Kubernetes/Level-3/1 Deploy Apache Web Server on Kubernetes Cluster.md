### Task
There is an application that needs to be deployed on Kubernetes cluster under Apache web server. The Nautilus application development team has asked the DevOps team to deploy it. We need to develop a template as per requirements mentioned below:

  1. Create a namespace named as httpd-namespace-xfusion.

  2. Create a deployment named as httpd-deployment-xfusion under newly created namespace. For the deployment use httpd image with latest tag only and remember to mention the tag i.e httpd:latest, and make sure replica counts are 2.

  3. Create a service named as httpd-service-xfusion under same namespace to expose the deployment, nodePort should be 30004.

Note: The kubectl utility on jump_host has been configured to work with the kubernetes cluster.

### Solution
```
apiVersion: v1
kind: Namespace
metadata:
  name: httpd-namespace-xfusion

---

apiVersion: v1
kind: Service
metadata:
  name: httpd-service-xfusion
  namespace: httpd-namespace-xfusion
spec:
  type: NodePort
  selector:
    app: httpd-xfusion
  ports:
  - port: 80
    targetPort: 80
    nodePort: 30004

---

apiVersion: apps/v1
kind: Deployment
metadata:
  name: http-deployment-xfusion
  namespace: httpd-namespace-xfusion
spec:
  replicas: 2
  selector:
    matchLabels:
      app: httpd-xfusion
  template:
    metadata:
      labels:
        app: httpd-xfusion
    spec:
      containers:
      - name: httpd-container-xfusion
        image: httpd:latest
        ports:
          - containerPort: 80
```

```
thor@jumphost ~$ kubectl apply -f httpd-deployment.yaml
namespace/httpd-namespace-xfusion created
service/httpd-service-xfusion created
deployment.apps/http-deployment-xfusion created

thor@jumphost ~$ kubectl -n httpd-namespace-xfusion get deployments -o wide
NAME                      READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS                IMAGES         SELECTOR
http-deployment-xfusion   2/2     2            2           14s   httpd-container-xfusion   httpd:latest   app=httpd-xfusion

thor@jumphost ~$ kubectl -n httpd-namespace-xfusion get pods -o wide
NAME                                       READY   STATUS    RESTARTS   AGE   IP           NODE                      NOMINATED NODE   READINESS GATES
http-deployment-xfusion-6fd56cb4d9-lvk8p   1/1     Running   0          21s   10.244.0.8   kodekloud-control-plane   <none>           <none>
http-deployment-xfusion-6fd56cb4d9-v7pfl   1/1     Running   0          21s   10.244.0.7   kodekloud-control-plane   <none>           <none>

thor@jumphost ~$ kubectl -n httpd-namespace-xfusion get service -o wide
NAME                    TYPE       CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE   SELECTOR
httpd-service-xfusion   NodePort   10.96.74.191   <none>        80:30004/TCP   27s   app=httpd-xfusion
```
