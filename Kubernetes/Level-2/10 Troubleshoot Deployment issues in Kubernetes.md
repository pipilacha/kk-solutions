### Task
Last week, the Nautilus DevOps team deployed a redis app on Kubernetes cluster, which was working fine so far. This morning one of the team members was making some changes in this existing setup, but he made some mistakes and the app went down. We need to fix this as soon as possible. Please take a look.


The deployment name is redis-deployment. The pods are not in running state right now, so please look into the issue and fix the same.

Note: The kubectl utility on jump_host has been configured to work with the kubernetes cluster.

### Solution

```
# Image is wrong
thor@jumphost ~$ kubectl get deployment redis-deployment -o wide
NAME               READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS        IMAGES        SELECTOR
redis-deployment   0/1     1            0           12m   redis-container   redis:alpin   app=redis

thor@jumphost ~$ kubectl set image deployment redis-deployment redis-container=redis:alpine

thor@jumphost ~$ kubectl get deployment redis-deployment -o wide
NAME               READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS        IMAGES         SELECTOR
redis-deployment   0/1     1            0           16m   redis-container   redis:alpine   app=redis

thor@jumphost ~$ kubectl get pods
NAME                                READY   STATUS              RESTARTS   AGE
redis-deployment-5b5b8f666f-st2zh   0/1     ContainerCreating   0          73s
redis-deployment-6fd9d5fcb-5hz75    0/1     ContainerCreating   0          17m


# Still some issues, container is not creating
# Let's decribe deployment to see if we can spot errors


thor@jumphost ~$ kubectl describe deployment redis-deployment 
Name:                   redis-deployment
Namespace:              default
CreationTimestamp:      Wed, 03 Jul 2024 20:24:46 +0000
Labels:                 app=redis
Annotations:            deployment.kubernetes.io/revision: 2
Selector:               app=redis
Replicas:               1 desired | 1 updated | 2 total | 0 available | 2 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  app=redis
  Containers:
   redis-container:
    Image:      redis:alpine
    Port:       6379/TCP
    Host Port:  0/TCP
    Requests:
      cpu:        300m
    Environment:  <none>
    Mounts:
      /redis-master from config (rw)
      /redis-master-data from data (rw)
  Volumes:
   data:
    Type:       EmptyDir (a temporary directory that shares a pod's lifetime)
    Medium:     
    SizeLimit:  <unset>
   config:
    Type:          ConfigMap (a volume populated by a ConfigMap)
    Name:          redis-cofig
    Optional:      false
  Node-Selectors:  <none>
  Tolerations:     <none>
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Available      False   MinimumReplicasUnavailable
  Progressing    True    ReplicaSetUpdated
OldReplicaSets:  redis-deployment-6fd9d5fcb (1/1 replicas created)
NewReplicaSet:   redis-deployment-5b5b8f666f (1/1 replicas created)
Events:
  Type    Reason             Age   From                   Message
  ----    ------             ----  ----                   -------
  Normal  ScalingReplicaSet  17m   deployment-controller  Scaled up replica set redis-deployment-6fd9d5fcb to 1
  Normal  ScalingReplicaSet  85s   deployment-controller  Scaled up replica set redis-deployment-5b5b8f666f to 1


# It doesnt tells us much
# let's describe pods, specifically the last one


thor@jumphost ~$ kubectl describe pods redis-deployment-6fd9d5fcb-5hz75
Name:             redis-deployment-6fd9d5fcb-5hz75
Namespace:        default
Priority:         0
Service Account:  default
Node:             kodekloud-control-plane/172.17.0.2
Start Time:       Wed, 03 Jul 2024 20:24:46 +0000
Labels:           app=redis
                  pod-template-hash=6fd9d5fcb
Annotations:      <none>
Status:           Pending
IP:               
IPs:              <none>
Controlled By:    ReplicaSet/redis-deployment-6fd9d5fcb
Containers:
  redis-container:
    Container ID:   
    Image:          redis:alpin
    Image ID:       
    Port:           6379/TCP
    Host Port:      0/TCP
    State:          Waiting
      Reason:       ContainerCreating
    Ready:          False
    Restart Count:  0
    Requests:
      cpu:        300m
    Environment:  <none>
    Mounts:
      /redis-master from config (rw)
      /redis-master-data from data (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-6wkqc (ro)
Conditions:
  Type              Status
  Initialized       True 
  Ready             False 
  ContainersReady   False 
  PodScheduled      True 
Volumes:
  data:
    Type:       EmptyDir (a temporary directory that shares a pod's lifetime)
    Medium:     
    SizeLimit:  <unset>
  config:
    Type:      ConfigMap (a volume populated by a ConfigMap)
    Name:      redis-cofig
    Optional:  false
  kube-api-access-6wkqc:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
QoS Class:                   Burstable
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type     Reason       Age                 From               Message
  ----     ------       ----                ----               -------
  Normal   Scheduled    41m                 default-scheduler  Successfully assigned default/redis-deployment-6fd9d5fcb-5hz75 to kodekloud-control-plane
  Warning  FailedMount  11m (x23 over 41m)  kubelet            MountVolume.SetUp failed for volume "config" : configmap "redis-cofig" not found
  Warning  FailedMount  60s (x18 over 39m)  kubelet            Unable to attach or mount volumes: unmounted volumes=[config], unattached volumes=[], failed to process volumes=[]: timed out waiting for the condition


# It cannot find configmap "redis-config". there is a typo


thor@jumphost ~$ kubectl get cm
NAME               DATA   AGE
kube-root-ca.crt   1      70m
redis-config       2      43m


# lets patch

thor@jumphost ~$ kubectl patch deployment redis-deployment -p '{"spec": {"template": {"spec": {"volumes": [{"name": "config", "configMap": {"name": "redis-config"}}]}}}}'
deployment.apps/redis-deployment patched

thor@jumphost ~$ kubectl get deployments -o wide
NAME               READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS        IMAGES         SELECTOR
redis-deployment   1/1     1            1           52m   redis-container   redis:alpine   app=redis

thor@jumphost ~$ kubectl get pods
NAME                                READY   STATUS        RESTARTS   AGE
redis-deployment-5b5b8f666f-st2zh   0/1     Terminating   0          37m
redis-deployment-7c8d4f6ddf-92dlg   1/1     Running       0          60s
```
