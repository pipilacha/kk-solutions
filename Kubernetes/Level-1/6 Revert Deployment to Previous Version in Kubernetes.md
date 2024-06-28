### Task
Earlier today, the Nautilus DevOps team deployed a new release for an application. However, a customer has reported a bug related to this recent release. Consequently, the team aims to revert to the previous version.

There exists a deployment named nginx-deployment; initiate a rollback to the previous revision.

Note: The kubectl utility on jump_host is configured to interact with the Kubernetes cluster.

#### Solution
1. List Rollout history for the deployment
```
thor@jumphost ~$ kubectl rollout history deployment nginx-deployment
deployment.apps/nginx-deployment 
REVISION  CHANGE-CAUSE
1         <none>
2         kubectl set image deployment nginx-deployment nginx-container=nginx:alpine --kubeconfig=/root/.kube/config --record=true
```
We are at revision 2, we want to go back to 1. Revision 2 has container image set to nginx:alpine

2. Undo the rollout
   - We can just go back one revision:
     ```kubectl rollout undo deployment nginx-deployment```
   - Or, we can specify to which revision to rollback to:
     ```kubectl rollout undo deployment nginx-deployment --to-revision=1```
3. New state of the deployment, contianer image changed:
   ```
   thor@jumphost ~$ kubectl get deployments -o wide
   NAME               READY   UP-TO-DATE   AVAILABLE   AGE     CONTAINERS        IMAGES       SELECTOR
   nginx-deployment   3/3     3            3           8m25s   nginx-container   nginx:1.16   app=nginx-app
   ```
