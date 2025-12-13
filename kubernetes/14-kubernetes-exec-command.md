### Explore the  `kubectl exec`  Command

The  `kubectl exec`  command is used to execute commands directly inside a container within a pod. It is particularly useful for debugging and inspecting container environments.

Run the following command to view the available options for  `kubectl exec`:

```bash
kubectl exec -h
```

You will see the following output:

```plaintext
Execute a command in a container.

Examples:
  # Get output from running the 'date' command from pod mypod, using the first container by default
  kubectl exec mypod -- date

  # Get output from running the 'date' command in ruby-container from pod mypod
  kubectl exec mypod -c ruby-container -- date

  # Switch to raw terminal mode; sends stdin to 'bash' in ruby-container from pod mypod
  # and sends stdout/stderr from 'bash' back to the client
  kubectl exec mypod -c ruby-container -i -t -- bash -il

  # List contents of /usr from the first container of pod mypod and sort by modification time
  # If the command you want to execute in the pod has any flags in common (e.g. -i),
  # you must use two dashes (--) to separate your command's flags/arguments
  # Also note, do not surround your command and its flags/arguments with quotes
  # unless that is how you would execute it normally (i.e., do ls -t /usr, not "ls -t /usr")
  kubectl exec mypod -i -t -- ls -t /usr

  # Get output from running 'date' command from the first pod of the deployment mydeployment, using the first container by default
  kubectl exec deploy/mydeployment -- date

  # Get output from running 'date' command from the first pod of the service myservice, using the first container by default
  kubectl exec svc/myservice -- date
```

### Executing a Command in a Container

In this step, you will learn how to execute a command in a container running in a pod.

1.  Start by creating a deployment with one replica and an Nginx container:
    
    ```bash
    kubectl create deployment nginx --image=nginx --replicas=1
    ```
    
2.  Wait for the pod to become ready:
    
    ```bash
    kubectl wait --for=condition=Ready pod -l app=nginx
    ```
    
3.  Use the  `kubectl exec`  command to execute a command inside the Nginx container:
    
    ```bash
    kubectl exec -it POD_NAME -- /bin/bash
    ```
    
    Replace  `POD_NAME`  with the name of the pod created in step 1, and you can get the  `POD_NAME`  with the  `kubectl get pod -l app=nginx`  command.

### Executing a Command in a Specific Container

In this step, you will learn how to execute a command in a specific container running in a pod with multiple containers.

1.  Create a pod with two containers: Nginx and BusyBox:
    
    ```bash
    cat << EOF | kubectl apply -f -
    apiVersion: v1
    kind: Pod
    metadata:
      name: nginx-busybox
    spec:
      containers:
      - name: nginx
        image: nginx
      - name: busybox
        image: busybox
        command:
          - sleep
          - "3600"
    EOF
    ```
    
2.  Wait for the pod to become ready:
    
    ```bash
    kubectl wait --for=condition=Ready pod nginx-busybox
    ```
    
3.  Use the  `kubectl exec`  command to execute a command inside the BusyBox container:
    
    ```bash
    kubectl exec nginx-busybox -c busybox -- ls /bin
    ```

### Executing a Command with a Tty

In this step, you will learn how to execute a command with a tty in a container.

1.  Use the  `kubectl exec`  command with the  `-it`  options to execute a command with a tty:
    
    ```bash
    kubectl exec -it nginx-busybox -c nginx -- /bin/sh
    ```
    
2.  Once inside the container shell, run a command:
    
    ```bash
    echo "Hello, world!"
    ```
### Executing a Command with Environment Variables

In this step, you will learn how to execute a command with environment variables inside a container.

1.  Create a deployment with one replica and an Nginx container with an environment variable:
    
    ```bash
    kubectl run nginx-env --image=nginx --env="MY_VAR=my-value"
    ```
    
2.  Wait for the pod to become ready:
    
    ```bash
    kubectl wait --for=condition=Ready pod -l run=nginx-env
    ```
    
3.  Use the  `kubectl exec`  command to execute a command inside the Nginx container that outputs the environment variable:
    
    ```bash
    kubectl exec nginx-env -- sh -c 'echo $MY_VAR'
    ```
    
    Replace nginx-env with the name of the pod created in step 1.