### Task
The Nautilus DevOps team is crafting jobs in the Kubernetes cluster. While they're developing actual scripts/commands, they're currently setting up templates and testing jobs with dummy commands. Please create a job template as per details given below:

    1. Create a job named countdown-devops.

    2. The spec template should be named countdown-devops (under metadata), and the container should be named container-countdown-devops

    3. Utilize image ubuntu with latest tag (ensure to specify as ubuntu:latest), and set the restart policy to Never.

    4. Execute the command sleep 5

Note: The kubectl utility on jump_host is set up to operate with the Kubernetes cluster.

### Solution
[Job](https://kubernetes.io/docs/concepts/workloads/controllers/job/)
1. Create config file. I named it ```countdown-job.yaml```
```
apiVersion: batch/v1
kind: Job
metadata:
  name: countdown-devops
spec:
  template:
    metadata:
      name: countdown-devops
    spec:
      containers:
      - name: container-countdown-devops
        image: ubuntu:latest
        command: ["/bin/sleep"]
        args: ["5"]
      restartPolicy: Never

thor@jumphost ~$ kubectl apply -f countdown-job.yaml 
job.batch/countdown-devops created

thor@jumphost ~$ kubectl get jobs -o wide
NAME               COMPLETIONS   DURATION   AGE   CONTAINERS                   IMAGES          SELECTOR
countdown-devops   1/1           13s        51s   container-countdown-devops   ubuntu:latest   batch.kubernetes.io/controller-uid=a9d2f9dd-a82a-404c-b9a5-b385be045c79
```
