### Viewing Container Logs

In this step, you will learn how to view the logs of a container running in a pod.

1.  Start by creating a deployment with one replica and an Nginx container:
    
    ```bash
    kubectl create deployment nginx --image=nginx --replicas=1
    ```
    
2.  Wait for the pod to become ready:
    
    ```bash
    kubectl wait --for=condition=Ready pod -l app=nginx
    ```
    
3.  Use the  `kubectl logs`  command to view the logs of the Nginx container:
    
    ```bash
    kubectl logs POD_NAME
    ```
    

Replace  `POD_NAME`  with the name of the pod created in step 1, and you can get the  `POD_NAME`  with the  `kubectl get pod -l app=nginx`  command.

### Viewing Logs from a Specific Container in a Pod

In this step, you will learn how to view the logs of a specific container running in a pod.

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
    
3.  Use the  `kubectl logs`  command to view the logs of the BusyBox container:
    
    ```bash
    kubectl logs nginx-busybox -c busybox
    ```

### Following Logs in Real-Time

In this step, you will learn how to follow logs in real-time as they are generated.

1.  Use the  `kubectl logs`  command with the  `-f`  option to follow logs in real-time:
    
    ```bash
    kubectl logs -f nginx-busybox
    ```
    
2.  Open a new terminal and create a shell in the Nginx container:
    
    ```bash
    kubectl exec -it nginx-busybox -c nginx -- /bin/sh
    ```
    
3.  Generate some logs by running a command inside the container:
    
    ```bash
    curl 127.0.0.1
    ```
    
4.  Switch back to the first terminal where you are following the logs and observe that the new log entry is displayed.

### Viewing Logs from Multiple Containers

In this step, you will learn how to view logs from multiple containers running in a pod.

1.  Create a pod with two containers: Nginx and Fluentd:
    
    ```bash
    cat << EOF | kubectl apply -f -
    apiVersion: v1
    kind: Pod
    metadata:
      name: nginx-fluentd
    spec:
      containers:
      - name: nginx
        image: nginx
      - name: fluentd
        image: fluentd
    EOF
    ```
    
2.  Wait for the pod to become ready:
    
    ```bash
    kubectl wait --for=condition=Ready pod nginx-fluentd
    ```
    
3.  Use the  `kubectl logs`  command to view logs from both containers:
    
    ```bash
    kubectl logs nginx-fluentd -c nginx
    kubectl logs nginx-fluentd -c fluentd
    ```
**What is the significance of the '--for=condition=Ready' flag in the 'kubectl wait' command?**

The  `--for=condition=Ready`  flag in the  `kubectl wait`  command is used to block until a specific condition is met for a Kubernetes resource. When you use this flag, it tells  `kubectl`  to wait until the specified resource (like a pod, deployment, or service) is in the "Ready" state.

For example, if you run the command:

```sh
kubectl wait --for=condition=Ready pod/my-pod
```

This command will wait until the pod named  `my-pod`  is ready to serve traffic, meaning it has passed all readiness checks defined in its configuration. This is particularly useful in automation scripts or CI/CD pipelines where you need to ensure that a resource is fully operational before proceeding with further actions.
