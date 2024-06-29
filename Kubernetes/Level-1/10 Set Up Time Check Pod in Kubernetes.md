### Task
The Nautilus DevOps team needs a time check pod created in a specific Kubernetes namespace for logging purposes. Initially, it's for testing, but it may be integrated into an existing cluster later. Here's what's required:

    Create a pod called time-check in the xfusion namespace. The pod should contain a container named time-check, utilizing the busybox image with the latest tag (specify as busybox:latest).

    Create a config map named time-config with the data TIME_FREQ=3 in the same namespace.

    Configure the time-check container to execute the command: while true; do date; sleep $TIME_FREQ;done. Ensure the result is written /opt/data/time/time-check.log. Also, add an environmental variable TIME_FREQ in the container, fetching its value from the config map TIME_FREQ key.

    Create a volume log-volume and mount it at /opt/data/time within the container.

Note: The kubectl utility on jump_host is configured to operate with the Kubernetes cluster.

### Solution
1. We must create the namespace because it does not exist. It does not specify if volumes has to be persistent, so I will use Ephemeral instead.
```
apiVersion: v1
kind: Namespace
metadata:
  name: xfusion
---
apiVersion: v1
kind: Pod
metadata:
  name: time-check
  namespace: xfusion
spec:
  containers:
  - name: time-check
    image: busybox:latest
    volumeMounts:
    - mountPath: "/opt/data/time"
      name: log-volume
    command: ["/bin/sh"]
    args: ["-c", "while true; do date >> /opt/data/time/time-check.log; sleep $TIME_FREQ; done"]
    env:
      - name: TIME_FREQ
        valueFrom:
          configMapKeyRef:
            name: time-config
            key: time_freq
  volumes:
    - name: log-volume
      emptyDir:
        sizeLimit: 500Mi
---
apiVersion: v1
kind: ConfigMap
metadata:
  name: time-config
  namespace: xfusion
data:
  time_freq: "3"
```
```
thor@jumphost ~$ kubectl apply -f time-check.yaml
namespace/xfusion created
pod/time-check created
configmap/time-config created

# We can se environment variable set, as well as the volume
thor@jumphost ~$ kubectl -n xfusion describe pod time-check
Name:             time-check
Namespace:        xfusion
Priority:         0
Service Account:  default
Node:             kodekloud-control-plane/172.17.0.2
Start Time:       Sat, 29 Jun 2024 02:31:34 +0000
Labels:           <none>
Annotations:      <none>
Status:           Running
IP:               10.244.0.8
IPs:
  IP:  10.244.0.8
Containers:
  time-check:
    Container ID:  containerd://d07311de0b048769ca5d6b92dfcc39e7100bc360e4d533ff6f807c988fa5ce3e
    Image:         busybox:latest
    Image ID:      docker.io/library/busybox@sha256:9ae97d36d26566ff84e8893c64a6dc4fe8ca6d1144bf5b87b2b85a32def253c7
    Port:          <none>
    Host Port:     <none>
    Command:
      /bin/sh
    Args:
      -c
      while true; do date >> /opt/data/time/time-check.log; sleep $TIME_FREQ; done
    State:          Running
      Started:      Sat, 29 Jun 2024 02:31:35 +0000
    Ready:          True
    Restart Count:  0
    Environment:
      TIME_FREQ:  <set to the key 'time_freq' of config map 'time-config'>  Optional: false
    Mounts:
      /opt/data/time from log-volume (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-gf65v (ro)
Conditions:
  Type              Status
  Initialized       True 
  Ready             True 
  ContainersReady   True 
  PodScheduled      True 
Volumes:
  log-volume:
    Type:       EmptyDir (a temporary directory that shares a pod's lifetime)
    Medium:     
    SizeLimit:  500Mi
  kube-api-access-gf65v:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason     Age   From               Message
  ----    ------     ----  ----               -------
  Normal  Scheduled  16s   default-scheduler  Successfully assigned xfusion/time-check to kodekloud-control-plane
  Normal  Pulling    15s   kubelet            Pulling image "busybox:latest"
  Normal  Pulled     15s   kubelet            Successfully pulled image "busybox:latest" in 291.026346ms (291.032613ms including waiting)
  Normal  Created    15s   kubelet            Created container time-check
  Normal  Started    15s   kubelet            Started container time-check
```
