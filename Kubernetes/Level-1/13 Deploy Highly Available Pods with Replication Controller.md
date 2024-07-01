### Task
The Nautilus DevOps team is establishing a ReplicationController to deploy multiple pods for hosting applications that require a highly available infrastructure. Follow the specifications below to create the ReplicationController:

  1. Create a ReplicationController using the httpd image, preferably with latest tag, and name it httpd-replicationcontroller.

  2. Assign labels app as httpd_app, and type as front-end. Ensure the container is named httpd-container and set the replica count to 3.

All pods should be running state post-deployment.

Note: The kubectl utility on jump_host is configured to operate with the Kubernetes cluster.

### Solution
1. Create config file:
  ```
  apiVersion: v1
  kind: ReplicationController
  metadata:
    name: httpd-replicationcontroller
  spec:
    replicas: 3
    selector:
      app: httpd_app
      type: front-end
    template:
      metadata:
        name: httpd-pod
        labels:
          app: httpd_app
          type: front-end
      spec:
        containers:
          - name: httpd-container
            image: httpd:latest
  ```

2. Apply config file:
  ```
  thor@jumphost ~$ kubectl apply -f httpd-rc.yaml
  replicationcontroller/httpd-replicationcontroller created

  thor@jumphost ~$ kubectl get pods
  NAME                                READY   STATUS    RESTARTS   AGE
  httpd-replicationcontroller-jjnp2   1/1     Running   0          13s
  httpd-replicationcontroller-x9xjt   1/1     Running   0          13s
  httpd-replicationcontroller-zlghk   1/1     Running   0          13s

  thor@jumphost ~$ kubectl get -o wide  rc 
 NAME                          DESIRED   CURRENT   READY   AGE   CONTAINERS        IMAGES         SELECTOR
 httpd-replicationcontroller   3         3         3       79s   httpd-container   httpd:latest   app=httpd_app,type=front-end
 ```
