### Task
The Nautilus DevOps team is setting up recurring tasks on different schedules. Currently, they're developing scripts to be executed periodically. To kickstart the process, they're creating cron jobs in the Kubernetes cluster with placeholder commands. Follow the instructions below:


    1. Create a cronjob named nautilus.

    2. Set Its schedule to something like */9 * * * *. You can set any schedule for now.

    3. Name the container cron-nautilus.

    3. Utilize the httpd image with latest tag (specify as httpd:latest).

    5. Execute the dummy command echo Welcome to xfusioncorp!.

    6. Ensure the restart policy is OnFailure.

Note: The kubectl utility on jump_host is configured to work with the kubernetes cluster.

### Solution
[CronJob example](https://kubernetes.io/docs/tasks/job/automated-tasks-with-cron-jobs/)
```
# config file named nautilus-cronjob.yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: nautilus
spec:
  schedule: "*/2 * * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: cron-nautilus
            image: httpd:latest
            command: ["/bin/echo"]
            args: ["Welcome to xfusioncorp!"]
          restartPolicy: OnFailure


thor@jumphost ~$ kubectl apply -f nautilus-cronjob.yaml
cronjob.batch/nautilus created

kubectl get cronjobs nautilus -o wide
NAME       SCHEDULE      SUSPEND   ACTIVE   LAST SCHEDULE   AGE   CONTAINERS      IMAGES         SELECTOR
nautilus   */2 * * * *   False     1        35s             15m   cron-nautilus   httpd:latest   <none>

# watch cron jobs
thor@jumphost ~$ kubectl get jobs --watch
NAME                COMPLETIONS   DURATION   AGE
nautilus-28660282   1/1           4s         2m35s
nautilus-28660284   1/1           3s         35s
```
