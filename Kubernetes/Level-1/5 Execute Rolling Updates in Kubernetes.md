### Task
An application currently running on the Kubernetes cluster employs the nginx web server. The Nautilus application development team has introduced some recent changes that need deployment. They've crafted an image nginx:1.19 with the latest updates.

Execute a rolling update for this application, integrating the nginx:1.19 image. The deployment is named nginx-deployment.

Ensure all pods are operational post-update.

Note: The kubectl utility on jump_host is set up to operate with the Kubernetes cluster

### Solution
1. Let's see deployment info:
```
thor@jumphost ~$ kubectl describe deployments nginx-deployment
Name:                   nginx-deployment
Namespace:              default
CreationTimestamp:      Fri, 28 Jun 2024 21:31:42 +0000
Labels:                 app=nginx-app
                        type=front-end
Annotations:            deployment.kubernetes.io/revision: 1
Selector:               app=nginx-app
Replicas:               3 desired | 3 updated | 3 total | 3 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  app=nginx-app
  Containers:
   nginx-container:
    Image:         nginx:1.16
....
```
StrategyType is already set to rolling. So we just need to edit the image.

We don't have the local file, we will have to get the config yaml from the running deployment or use ```kubectl set image```

### Approach 1. ```kubectl edit deployment nginx-deployment -o yaml``` 
This command will allows to edit the deployment in yaml format. It will open an editor with the configuration, we just make the changes and save it. It will apply changes inmediately after closing editor.
```
thor@jumphost ~$ kubectl describe deployments nginx-deployment
Name:                   nginx-deployment
Namespace:              default
CreationTimestamp:      Fri, 28 Jun 2024 21:31:42 +0000
Labels:                 app=nginx-app
                        type=front-end
Annotations:            deployment.kubernetes.io/revision: 2
Selector:               app=nginx-app
Replicas:               3 desired | 3 updated | 3 total | 3 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  app=nginx-app
  Containers:
   nginx-container:
    Image:         nginx:1.19
    Port:          <none>
    Host Port:     <none>
    Environment:   <none>
    Mounts:        <none>
  Volumes:         <none>
  Node-Selectors:  <none>
  Tolerations:     <none>
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Available      True    MinimumReplicasAvailable
  Progressing    True    NewReplicaSetAvailable
OldReplicaSets:  nginx-deployment-989f57c54 (0/0 replicas created)
NewReplicaSet:   nginx-deployment-dc49f85cc (3/3 replicas created)
Events:
  Type    Reason             Age    From                   Message
  ----    ------             ----   ----                   -------
  Normal  ScalingReplicaSet  6m28s  deployment-controller  Scaled up replica set nginx-deployment-989f57c54 to 3
  Normal  ScalingReplicaSet  13s    deployment-controller  Scaled up replica set nginx-deployment-dc49f85cc to 1
  Normal  ScalingReplicaSet  4s     deployment-controller  Scaled down replica set nginx-deployment-989f57c54 to 2 from 3
  Normal  ScalingReplicaSet  4s     deployment-controller  Scaled up replica set nginx-deployment-dc49f85cc to 2 from 1
  Normal  ScalingReplicaSet  2s     deployment-controller  Scaled down replica set nginx-deployment-989f57c54 to 1 from 2
  Normal  ScalingReplicaSet  2s     deployment-controller  Scaled up replica set nginx-deployment-dc49f85cc to 3 from 2
  Normal  ScalingReplicaSet  0s     deployment-controller  Scaled down replica set nginx-deployment-989f57c54 to 0 from 1
```
### Approach 2: ```kubectl set image```
Here we are setting the image of the container ```nginx-container``` of the deployment ```nginx-deployment``` to the new image.
```
thor@jumphost ~$ kubectl set image deployment nginx-deployment nginx-container=nginx:1.17
deployment.apps/nginx-deployment image updated

thor@jumphost ~$ kubectl describe deployments
Name:                   nginx-deployment
Namespace:              default
CreationTimestamp:      Fri, 28 Jun 2024 21:42:56 +0000
Labels:                 app=nginx-app
                        type=front-end
Annotations:            deployment.kubernetes.io/revision: 2
Selector:               app=nginx-app
Replicas:               3 desired | 3 updated | 3 total | 3 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  app=nginx-app
  Containers:
   nginx-container:
    Image:         nginx:1.17
    Port:          <none>
    Host Port:     <none>
    Environment:   <none>
    Mounts:        <none>
  Volumes:         <none>
  Node-Selectors:  <none>
  Tolerations:     <none>
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Available      True    MinimumReplicasAvailable
  Progressing    True    NewReplicaSetAvailable
OldReplicaSets:  nginx-deployment-989f57c54 (0/0 replicas created)
NewReplicaSet:   nginx-deployment-5dd558cf95 (3/3 replicas created)
Events:
  Type    Reason             Age    From                   Message
  ----    ------             ----   ----                   -------
  Normal  ScalingReplicaSet  6m34s  deployment-controller  Scaled up replica set nginx-deployment-989f57c54 to 3
  Normal  ScalingReplicaSet  20s    deployment-controller  Scaled up replica set nginx-deployment-5dd558cf95 to 1
  Normal  ScalingReplicaSet  14s    deployment-controller  Scaled down replica set nginx-deployment-989f57c54 to 2 from 3
  Normal  ScalingReplicaSet  14s    deployment-controller  Scaled up replica set nginx-deployment-5dd558cf95 to 2 from 1
  Normal  ScalingReplicaSet  12s    deployment-controller  Scaled down replica set nginx-deployment-989f57c54 to 1 from 2
  Normal  ScalingReplicaSet  12s    deployment-controller  Scaled up replica set nginx-deployment-5dd558cf95 to 3 from 2
  Normal  ScalingReplicaSet  10s    deployment-controller  Scaled down replica set nginx-deployment-989f57c54 to 0 from 1
```

