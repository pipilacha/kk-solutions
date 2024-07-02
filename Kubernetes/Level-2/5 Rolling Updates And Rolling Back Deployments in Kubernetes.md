### Task
There is a production deployment planned for next week. The Nautilus DevOps team wants to test the deployment update and rollback on Dev environment first so that they can identify the risks in advance. Below you can find more details about the plan they want to execute.


  1. Create a namespace datacenter. Create a deployment called httpd-deploy under this new namespace, It should have one container called httpd, use httpd:2.4.27 image and 2 replicas. The deployment should use RollingUpdate strategy with maxSurge=1, and maxUnavailable=2. Also create a NodePort type service named httpd-service and expose the deployment on nodePort: 30008.

  2. Now upgrade the deployment to version httpd:2.4.43 using a rolling update.

  3. Finally, once all pods are updated undo the recent update and roll back to the previous/original version.

Note:

a. The kubectl utility on jump_host has been configured to work with the kubernetes cluster.

b. Please make sure you only use the specified image(s) for this deployment and as per the sequence mentioned in the task description. If you mistakenly use a wrong image and fix it later, that will also distort the revision history which can eventually fail this task.

### Solution
1. Create config file and apply:
```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: datacenter

---

apiVersion: apps/v1
kind: Deployment
metadata:
  name: httpd-deploy
  namespace: datacenter
spec:
  replicas: 2
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 2
  selector:
    matchLabels:
      app: httpd
  template:
    metadata:
      labels:
        app: httpd
    spec:
      containers:
      - name: httpd
        image: httpd:2.4.27
--- 

apiVersion: v1
kind: Service
metadata:
  name: httpd-service
  namespace: datacenter
spec: 
  type: NodePort
  selector:
    app: httpd
  ports:
  - port: 80
    targetPort: 80
    nodePort: 30008
```
```bash
thor@jumphost ~$ kubectl apply -f datacenter-deployment.yaml
namespace/datacenter created
deployment.apps/httpd-deploy created
service/httpd-service created

thor@jumphost ~$ kubectl -n datacenter get deployments -o wide
NAME           READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS   IMAGES         SELECTOR
httpd-deploy   2/2     2            2           23s   httpd        httpd:2.4.27   app=httpd

thor@jumphost ~$ kubectl -n datacenter get pods -o wide
NAME                            READY   STATUS    RESTARTS   AGE   IP           NODE                      NOMINATED NODE   READINESS GATES
httpd-deploy-6bb5ccd5b4-6c5tl   1/1     Running   0          70s   10.244.0.6   kodekloud-control-plane   <none>           <none>
httpd-deploy-6bb5ccd5b4-w97qv   1/1     Running   0          70s   10.244.0.5   kodekloud-control-plane   <none>           <none>
thor@jumphost ~$ kubectl -n datacenter get services -o wide

NAME            TYPE       CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE    SELECTOR
httpd-service   NodePort   10.96.119.99   <none>        80:30008/TCP   100s   app=httpd
```

2. Change image to ```httpd:2.4.43```
```bash
thor@jumphost ~$ kubectl -n datacenter set image deployment httpd-deploy httpd=httpd:2.4.43deployment.apps/httpd-deploy image updated

thor@jumphost ~$ kubectl -n datacenter get deployments -o wide
NAME           READY   UP-TO-DATE   AVAILABLE   AGE     CONTAINERS   IMAGES         SELECTOR
httpd-deploy   2/2     2            2           4m57s   httpd        httpd:2.4.43   app=httpd
```

3. Rollback to previous version
```bash
thor@jumphost ~$ kubectl -n datacenter rollout history deployment httpd-deploy
deployment.apps/httpd-deploy 
REVISION  CHANGE-CAUSE
1         <none>
2         <none>

thor@jumphost ~$ kubectl -n datacenter rollout undo deployments httpd-deploy --to-revision=1
deployment.apps/httpd-deploy rolled back

thor@jumphost ~$ kubectl -n datacenter get deployments -o wide
NAME           READY   UP-TO-DATE   AVAILABLE   AGE     CONTAINERS   IMAGES         SELECTOR
httpd-deploy   2/2     2            2           9m12s   httpd        httpd:2.4.27   app=httpd
```
