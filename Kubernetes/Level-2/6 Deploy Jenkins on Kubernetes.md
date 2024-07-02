### Task
The Nautilus DevOps team is planning to set up a Jenkins CI server to create/manage some deployment pipelines for some of the projects. They want to set up the Jenkins server on Kubernetes cluster. Below you can find more details about the task:

1) Create a namespace jenkins

2) Create a Service for jenkins deployment. Service name should be jenkins-service under jenkins namespace, type should be NodePort, nodePort should be 30008

3) Create a Jenkins Deployment under jenkins namespace, It should be name as jenkins-deployment , labels app should be jenkins , container name should be jenkins-container , use jenkins/jenkins image , containerPort should be 8080 and replicas count should be 1.

Make sure to wait for the pods to be in running state and make sure you are able to access the Jenkins login screen in the browser before hitting the Check button.

Note: The kubectl utility on jump_host has been configured to work with the kubernetes cluster.

### Solution
Create config file and apply
```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: jenkins

---

apiVersion: v1
kind: Service
metadata:
  name: jenkins-service
  namespace: jenkins
spec: 
  type: NodePort
  selector:
    app: jenkins
  ports:
  - port 8080
    targetPort: 8080
    nodePort: 30008

---

apiVersion: apps/v1
kind: Deployment
metadata:
  name: jenkins-deployment
  namespace: jenkins
spec:
  replicas: 1
  selector:
    matchLabels:
      app: jenkins
  template:
    metadata: 
      labels:
        app: jenkins
    spec:
      containers:
      - name: jenkins-container
        image: jenkins/jenkins
        ports:
          - containerPort: 8080
```
```
thor@jumphost ~$ kubectl -n jenkins get deployments -o wide
NAME                 READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS          IMAGES            SELECTOR
jenkins-deployment   1/1     1            1           78s   jenkins-container   jenkins/jenkins   app=jenkins

thor@jumphost ~$ kubectl -n jenkins  get pods
NAME                                  READY   STATUS    RESTARTS   AGE
jenkins-deployment-667887d68c-m2ztn   1/1     Running   0          4m8s

thor@jumphost ~$ kubectl -n jenkins logs jenkins-deployment-667887d68c-m2ztn | tail
This may also be found at: /var/jenkins_home/secrets/initialAdminPassword

*************************************************************
*************************************************************
*************************************************************

2024-07-02 23:13:30.074+0000 [id=69]    INFO    jenkins.InitReactorRunner$1#onAttained: Completed initialization
2024-07-02 23:13:30.280+0000 [id=37]    INFO    hudson.lifecycle.Lifecycle#onReady: Jenkins is fully up and running
2024-07-02 23:13:30.384+0000 [id=136]   INFO    h.m.DownloadService$Downloadable#load: Obtained the updated data file for hudson.tasks.Maven.MavenInstaller
2024-07-02 23:13:30.384+0000 [id=136]   INFO    hudson.util.Retrier#start: Performed the action check updates server successfully at the attempt #1
