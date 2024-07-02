### Task
The Nautilus DevOps teams is planning to set up a Grafana tool to collect and analyze analytics from some applications. They are planning to deploy it on Kubernetes cluster. Below you can find more details.


1.) Create a deployment named grafana-deployment-devops using any grafana image for Grafana app. Set other parameters as per your choice.

2.) Create NodePort type service with nodePort 32000 to expose the app.

You need not to make any configuration changes inside the Grafana app once deployed, just make sure you are able to access the Grafana login page.

Note: The kubectl on jump_host has been configured to work with kubernetes cluster.

### Solution
Create config file and apply. We will leave default port for Grafana (3000)
```yaml
apiVersion: v1
kind: Service
metadata:
  name: grafana-service
spec:
  type: NodePort
  selector:
    app: grafana-devops
  ports:
  - port: 3000
    targetPort: 3000
    nodePort: 32000
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: grafana-deployment-devops
  labels:
    app: grafana-devops
spec:
  replicas: 1
  selector:
    matchLabels:
      app: grafana-devops
  template:
    metadata:
      labels:
        app: grafana-devops
    spec:
      containers:
      - name: grafana-container-devops
        image: grafana/grafana
```

```
thor@jumphost ~$ kubectl apply -f grafana-deploy.yaml
service/grafana-service created
deployment.apps/grafana-deployment-devops created

thor@jumphost ~$ kubectl get deployments -o wide
NAME                        READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS                 IMAGES            SELECTOR
grafana-deployment-devops   1/1     1            1           52s   grafana-container-devops   grafana/grafana   app=grafana-devops
```
![image](https://github.com/pipilacha/kk-solutions/assets/44210264/c8115c48-fdd0-40dc-8e37-b64fae506d49)
