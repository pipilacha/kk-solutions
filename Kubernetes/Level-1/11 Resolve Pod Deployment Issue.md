### Task
A junior DevOps team member encountered difficulties deploying a stack on the Kubernetes cluster. The pod fails to start, presenting errors. Let's troubleshoot and rectify the issue promptly.

    1.There is a pod named webserver, and the container within it is named httpd-container, its utilizing the httpd:latest image.

    2. Additionally, there's a sidecar container named sidecar-container using the ubuntu:latest image.

Identify and address the issue to ensure the pod is in the running state and the application is accessible.

Note: The kubectl utility on jump_host is configured to interact with the Kubernetes cluster.

### Solution
1. Let's get pod and see the status
   ```
   thor@jumphost ~$ kubectl get pod webserver -o wide
   NAME        READY   STATUS             RESTARTS   AGE     IP           NODE                      NOMINATED NODE   READINESS GATES
   webserver   1/2     ImagePullBackOff   0          2m14s   10.244.0.5   kodekloud-control-plane   <none>           <none>
   ```
   OK, seems to be an error with an image pull

2. Let's describe pod and see more info
  ```
  thor@jumphost ~$ kubectl describe pod webserver
  Name:             webserver
  Namespace:        default
  Priority:         0
  Service Account:  default
  Node:             kodekloud-control-plane/172.17.0.2
  Start Time:       Sat, 29 Jun 2024 02:43:35 +0000
  Labels:           app=web-app
  Annotations:      <none>
  Status:           Pending
  IP:               10.244.0.5
  IPs:
    IP:  10.244.0.5
  Containers:
    httpd-container:
      Container ID:   
      Image:          httpd:latests
      Image ID:       
      Port:           <none>
      Host Port:      <none>
      State:          Waiting
        Reason:       ErrImagePull
      Ready:          False
      Restart Count:  0
      Environment:    <none>
      Mounts:
        /var/log/httpd from shared-logs (rw)
        /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-vc58f (ro)
    sidecar-container:
      Container ID:  containerd://7aa47c520d6291a2dc3677eec51796063afd68f560a2963ce94cc10044199e4e
      Image:         ubuntu:latest
      Image ID:      docker.io/library/ubuntu@sha256:2e863c44b718727c860746568e1d54afd13b2fa71b160f5cd9058fc436217b30
      Port:          <none>
      Host Port:     <none>
      Command:
        sh
        -c
        while true; do cat /var/log/httpd/access.log /var/log/httpd/error.log; sleep 30; done
      State:          Running
        Started:      Sat, 29 Jun 2024 02:43:41 +0000
      Ready:          True
      Restart Count:  0
      Environment:    <none>
      Mounts:
        /var/log/httpd from shared-logs (rw)
        /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-vc58f (ro)
  Conditions:
    Type              Status
    Initialized       True 
    Ready             False 
    ContainersReady   False 
    PodScheduled      True 
  Volumes:
    shared-logs:
      Type:       EmptyDir (a temporary directory that shares a pod's lifetime)
      Medium:     
      SizeLimit:  <unset>
    kube-api-access-vc58f:
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
    Type     Reason     Age                    From               Message
    ----     ------     ----                   ----               -------
    Normal   Scheduled  3m26s                  default-scheduler  Successfully assigned default/webserver to kodekloud-control-plane
    Normal   Pulling    3m25s                  kubelet            Pulling image "ubuntu:latest"
    Normal   Pulled     3m21s                  kubelet            Successfully pulled image "ubuntu:latest" in 4.320408299s (4.320422663s including waiting)
    Normal   Created    3m21s                  kubelet            Created container sidecar-container
    Normal   Started    3m20s                  kubelet            Started container sidecar-container
    Normal   Pulling    2m41s (x3 over 3m25s)  kubelet            Pulling image "httpd:latests"
    Warning  Failed     2m40s (x3 over 3m25s)  kubelet            Failed to pull image "httpd:latests": rpc error: code = NotFound desc = failed to pull and unpack image "docker.io/library/httpd:latests": failed to resolve reference "docker.io/library/httpd:latests": docker.io/library/httpd:latests: not found
    Warning  Failed     2m40s (x3 over 3m25s)  kubelet            Error: ErrImagePull
    Normal   BackOff    2m2s (x6 over 3m19s)   kubelet            Back-off pulling image "httpd:latests"
    Warning  Failed     2m2s (x6 over 3m19s)   kubelet            Error: ImagePullBackOff
  ```
  It is trying to pull "httpd:latests" which has a typo
  
3. Let's fix config for container with:  ```kubectl set image```
  ```
  thor@jumphost ~$ kubectl set image pod webserver httpd-container="httpd:latest"
  pod/webserver image updated
  
  thor@jumphost ~$ kubectl get pod webserver -o wide
  NAME        READY   STATUS    RESTARTS   AGE     IP           NODE                      NOMINATED NODE   READINESS GATES
  webserver   2/2     Running   0          8m28s   10.244.0.5   kodekloud-control-plane   <none>           <none>
  ```
