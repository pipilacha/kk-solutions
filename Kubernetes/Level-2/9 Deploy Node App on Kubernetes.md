### Task
The Nautilus development team has completed development of one of the node applications, which they are planning to deploy on a Kubernetes cluster. They recently had a meeting with the DevOps team to share their requirements. Based on that, the DevOps team has listed out the exact requirements to deploy the app. Find below more details:

   1. Create a deployment using gcr.io/kodekloud/centos-ssh-enabled:node image, replica count must be 2.

   2. Create a service to expose this app, the service type must be NodePort, targetPort must be 8080 and nodePort should be 30012.

   3. Make sure all the pods are in Running state after the deployment.

   4. You can check the application by clicking on NodeApp button on top bar.

You can use any labels as per your choice.

Note: The kubectl on jump_host has been configured to work with the kubernetes cluster.

### Solution
Create config and apply:
```yaml
apiVersion: v1
kind: Service
metadata:
  name: node-service-devops
spec:
  type: NodePort
  selector:
    app: node-deploy
  ports:
  - port: 8080
    targetPort: 8080
    nodePort: 30012

---

apiVersion: apps/v1
kind: Deployment
metadata:
  name: node-deployment-devops
  labels:
    app: node-deploy
spec:
  replicas: 2
  selector:
    matchLabels:
      app: node-deploy
  template:
    metadata:
      labels:
        app: node-deploy
    spec:
      containers:
      - name: node-container-devops
        image: gcr.io/kodekloud/centos-ssh-enabled:node
        ports:
        - containerPort: 8080
```

```
thor@jumphost ~$ kubectl apply -f node-deploy.yaml
service/node-service-devops created
deployment.apps/node-deployment-devops created

thor@jumphost ~$ kubectl get deployments -o wide
NAME                     READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS              IMAGES                                     SELECTOR
node-deployment-devops   2/2     2            2           56s   node-container-devops   gcr.io/kodekloud/centos-ssh-enabled:node   app=node-deploy
```
![image](https://github.com/pipilacha/kk-solutions/assets/44210264/bbb11f69-5006-4fef-9115-bf5d22e61dcb)
