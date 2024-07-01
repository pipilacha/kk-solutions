### Task
We encountered an issue with our Nginx and PHP-FPM setup on the Kubernetes cluster this morning, which halted its functionality. Investigate and rectify the issue:

The pod name is ```nginx-phpfpm``` and configmap name is ```nginx-config```. Identify and fix the problem.

Once resolved, copy ```/home/thor/index.php``` file from the ```jump host``` to the ```nginx-container``` within the nginx document root. After this, you should be able to access the website using ```Website``` button on the top bar.

Note: The kubectl utility on jump_host is configured to operate with the Kubernetes cluster.

### Solution
1. Let's check the configmap configuration:
  ```
  thor@jumphost ~$ kubectl describe cm nginx-config

  Name:         nginx-config
  Namespace:    default
  Labels:       <none>
  Annotations:  <none>
  
  Data
  ====
  nginx.conf:
  ----
  events {
  }
  http {
    server {
      listen 8099 default_server;
      listen [::]:8099 default_server;
  
      # Set nginx to serve files from the shared volume!
      root /var/www/html;
      index  index.html index.htm index.php;
      server_name _;
      location / {
        try_files $uri $uri/ =404;
      }
      location ~ \.php$ {
        include fastcgi_params;
        fastcgi_param REQUEST_METHOD $request_method;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_pass 127.0.0.1:9000;
      }
    }
  }
  
  
  BinaryData
  ====
  
  Events:  <none>
  ```
  We have a key ```nginx.conf``` with it's value, the file content.

  2. Let's check how the configmap is configured in the pod:
  ```
  thor@jumphost ~$ kubectl get -o yaml pod nginx-phpfpm
  ...

  spec:
    containers:
    - image: nginx:latest
      imagePullPolicy: Always
      name: nginx-container
      resources: {}
      terminationMessagePath: /dev/termination-log
      terminationMessagePolicy: File
      volumeMounts:
      - mountPath: /usr/share/nginx/html
        name: shared-files
      - mountPath: /etc/nginx/nginx.conf
        name: nginx-config-volume
        subPath: nginx.conf
      - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
        name: kube-api-access-lq6x4
        readOnly: true
  
  ...
  
  volumes:
    - configMap:
        defaultMode: 420
        name: nginx-config
      name: nginx-config-volume
  
  ...
  ```
The shared files mount path should be ```/var/www/html```

3. Patch or edit commands will not allow to edit the mount paths, we need to delete the resource and create a new one.
  We will save config into a file, edit it and use ```replace``` with the ```--force``` flag. This will delete the resource and create a new one.
  ```
  thor@jumphost ~$ kubectl get -o yaml pod nginx-phpfpm > nginx-phpfpm.yaml.old
  
  thor@jumphost ~$ sed 's*/usr/share/nginx/html*/var/www/html*g' nginx-phpfpm.yaml.old > nginx-phpfpm.yaml

  thor@jumphost ~$ kubectl replace -f nginx-phpfpm.yaml --force
  ```

5. Copy to nginx document root:
  ```
  thor@jumphost ~$ kubectl cp ~/index.php nginx-phpfpm:/var/www/html -c nginx-container
  ```
7. And done:
![image](https://github.com/pipilacha/kk-solutions/assets/44210264/d1482f4c-ee7a-4b58-a820-04c6948ea15a)

