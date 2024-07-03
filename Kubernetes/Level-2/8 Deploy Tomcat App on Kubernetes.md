### Task
A new java-based application is ready to be deployed on a Kubernetes cluster. The development team had a meeting with the DevOps team to share the requirements and application scope. The team is ready to setup an application stack for it under their existing cluster. Below you can find the details for this:

  1. Create a namespace named tomcat-namespace-devops.

  2. Create a deployment for tomcat app which should be named as tomcat-deployment-devops under the same namespace you created. Replica count should be 1, the container should be named as tomcat-container-devops, its image should be gcr.io/kodekloud/centos-ssh-enabled:tomcat and its container port should be 8080.

  3. Create a service for tomcat app which should be named as tomcat-service-devops under the same namespace you created. Service type should be NodePort and nodePort should be 32227.

Before clicking on Check button please make sure the application is up and running.

You can use any labels as per your choice.

Note: The kubectl on jump_host has been configured to work with the kubernetes cluster.


### Solution
Create config and apply:
```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: tomcat-namespace-devops

---

apiVersion: v1
kind: Service
metadata:
  name: tomcat-service-devops
  namespace: tomcat-namespace-devops
spec:
  type: NodePort
  selector:
    app: tomcat-deploy
  ports:
  - port: 8080
    targetPort: 8080
    nodePort: 32227

---

apiVersion: apps/v1
kind: Deployment
metadata:
  name: tomcat-deployment-devops
  namespace: tomcat-namespace-devops
  labels:
    app: tomcat-deploy
spec:
  replicas: 1
  selector:
    matchLabels:
      app: tomcat-deploy
  template:
    metadata:
      labels:
        app: tomcat-deploy
    spec:
      containers:
      - name: tomcat-container-devops
        image: gcr.io/kodekloud/centos-ssh-enabled:tomcat
```

```
thor@jumphost ~$ kubectl apply -f tomcat-deploy.yaml
namespace/tomcat-namespace-devops created
service/tomcat-service-devops created
deployment.apps/tomcat-deployment-devops created

thor@jumphost ~$ kubectl -n tomcat-namespace-devops get deployments -o wide
NAME                       READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS                IMAGES                                       SELECTOR
tomcat-deployment-devops   1/1     1            1           90s   tomcat-container-devops   gcr.io/kodekloud/centos-ssh-enabled:tomcat   app=tomcat-deploy
```
![image](https://github.com/pipilacha/kk-solutions/assets/44210264/8171dfea-0f00-41fb-9ad3-c16723a13311)
