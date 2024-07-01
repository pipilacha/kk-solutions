### Task
We are working on an application that will be deployed on multiple containers within a pod on Kubernetes cluster. There is a requirement to share a volume among the containers to save some temporary data. The Nautilus DevOps team is developing a similar template to replicate the scenario. Below you can find more details about it.

   1. Create a pod named volume-share-xfusion.

   2. For the first container, use image ubuntu with latest tag only and remember to mention the tag i.e ubuntu:latest, container should be named as volume-container-xfusion-1, and run a sleep command for it so that it remains in running state. Volume volume-share should be mounted at path /tmp/media.

   3. For the second container, use image ubuntu with the latest tag only and remember to mention the tag i.e ubuntu:latest, container should be named as volume-container-xfusion-2, and again run a sleep command for it so that it remains in running state. Volume volume-share should be mounted at path /tmp/demo.

   4. Volume name should be volume-share of type emptyDir.

   5. After creating the pod, exec into the first container i.e volume-container-xfusion-1, and just for testing create a file media.txt with any content under the mounted path of first container i.e /tmp/media.

   6. The file media.txt should be present under the mounted path /tmp/demo on the second container volume-container-xfusion-2 as well, since they are using a shared volume.

Note: The kubectl utility on jump_host has been configured to work with the kubernetes cluster.

### Solution
1. Create config file and apply:
  ```
  # Config file
  apiVersion: v1
  kind: Pod
  metadata:
    name: volume-share-xfusion
  spec:
    containers:
      - name: volume-container-xfusion-1
        image: ubuntu:latest
        command:
          - /bin/sleep
          - 10m
        volumeMounts:
          - mountPath: /tmp/media
            name: volume-share
  
      - name: volume-container-xfusion-2
        image: ubuntu:latest
        command:
          - /bin/sleep
          - 10m
        volumeMounts:
          - mountPath: /tmp/demo
            name: volume-share
  
    volumes:
      - name: volume-share
        emptyDir: {}
  
  thor@jumphost ~$ kubectl apply -f deployment.yaml 
  pod/volume-share-xfusion created
  
  thor@jumphost ~$ kubectl get pods -o wide
  NAME                   READY   STATUS    RESTARTS   AGE   IP           NODE                      NOMINATED NODE   READINESS GATES
  volume-share-xfusion   2/2     Running   0          79s   10.244.0.6   kodekloud-control-plane   <none>           <none>

  thor@jumphost ~$ kubectl get pods -o wide
  NAME                   READY   STATUS    RESTARTS   AGE   IP           NODE                      NOMINATED NODE   READINESS GATES
  volume-share-xfusion   2/2     Running   0          79s   10.244.0.6   kodekloud-control-plane   <none>           <none>
  ```
2. Create file and verify it is accessible in the other container:
  ```
  thor@jumphost ~$ kubectl exec volume-share-xfusion -c volume-container-xfusion-1 -i -t -- bash -il
  root@volume-share-xfusion:/# touch /tmp/media/media.txt
  root@volume-share-xfusion:/# exit
  logout
  
  thor@jumphost ~$ kubectl exec volume-share-xfusion -c volume-container-xfusion-2 -i -t -- bash -il
  root@volume-share-xfusion:/# ls -la /tmp/demo/
  total 8
  drwxrwxrwx 2 root root 4096 Jul  1 22:00 .
  drwxrwxrwt 1 root root 4096 Jul  1 21:53 ..
  -rw-r--r-- 1 root root    0 Jul  1 22:00 media.txt
  root@volume-share-xfusion:/# 
  logout
  ```
