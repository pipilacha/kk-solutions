### Task
The Nautilus DevOps team is working on to setup some pre-requisites for an application that will send the greetings to different users. There is a sample deployment, that needs to be tested. Below is a scenario which needs to be configured on Kubernetes cluster. Please find below more details about it.

  1. Create a pod named print-envars-greeting.

  2. Configure spec as, the container name should be print-env-container and use bash image.

  3. Create three environment variables:

a. GREETING and its value should be Welcome to

b. COMPANY and its value should be Stratos

c. GROUP and its value should be Datacenter

  4. Use command ["/bin/sh", "-c", 'echo "$(GREETING) $(COMPANY) $(GROUP)"'] (please use this exact command), also set its restartPolicy policy to Never to avoid crash loop back.

  5. You can check the output using kubectl logs -f print-envars-greeting command.

Note: The kubectl utility on jump_host has been configured to work with the kubernetes cluster.

### Solution
Config file:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: print-envars-greeting
spec:
  containers:
  - name: print-env-container
    image: bash:latest
    env:
    - name: GREETING
      value: "Welcome to"
    - name: COMPANY
      value: "Stratos"
    - name: GROUP
      value: "Datacenter"
    command: ["/bin/sh"]
    args: [ "-c", 'echo
```
```bash
thor@jumphost ~$ kubectl apply -f pod.yaml
pod/print-envars-greeting created

thor@jumphost ~$ kubectl get pods
NAME                    READY   STATUS      RESTARTS   AGE
print-envars-greeting   0/1     Completed   0          13s

thor@jumphost ~$ kubectl logs pods/print-envars-greeting
Welcome to Stratos Datacenter
```
