### Explore the  `kubectl port-forward`  Command

The  `kubectl port-forward`  command allows you to forward one or more local ports to a pod, deployment, or service in your Kubernetes cluster. It is commonly used for testing and debugging services without exposing them externally.

Run the following command to view the available options for  `kubectl port-forward`:

```bash
kubectl port-forward -h
```

You will see the following output:

```bash
Forward one or more local ports to a pod.

Use resource type/name such as deployment/mydeployment to select a pod. Resource type defaults to 'pod' if omitted.

If there are multiple pods matching the criteria, a pod will be selected automatically. The forwarding session ends
when the selected pod terminates, and a rerun of the command is needed to resume forwarding.

Examples:
  # Listen on ports 5000 and 6000 locally, forwarding data to/from ports 5000 and 6000 in the pod
  kubectl port-forward pod/mypod 5000 6000

  # Listen on ports 5000 and 6000 locally, forwarding data to/from ports 5000 and 6000 in a pod selected by the deployment
  kubectl port-forward deployment/mydeployment 5000 6000

  # Listen on port 8443 locally, forwarding to the targetPort of the service's port named "https" in a pod selected by the service
  kubectl port-forward service/myservice 8443:https

  # Listen on port 8888 locally, forwarding to 5000 in the pod
  kubectl port-forward pod/mypod 8888:5000

  # Listen on port 8888 on all addresses, forwarding to 5000 in the pod
  kubectl port-forward --address 0.0.0.0 pod/mypod 8888:5000

  # Listen on port 8888 on localhost and selected IP, forwarding to 5000 in the pod
  kubectl port-forward --address localhost,10.19.21.23 pod/mypod 8888:5000

  # Listen on a random port locally, forwarding to 5000 in the pod
  kubectl port-forward pod/mypod :5000
```

### Forwarding a Local Port to a Pod - *Important*

In this step, you will learn how to forward a local port to a port on a pod. This is useful for debugging applications or accessing services that are not exposed outside the cluster.

Note about terminal management:

-   The  `kubectl port-forward`  command will keep running in your terminal and block it from other uses
-   You'll need to open a new terminal window to run additional commands while port-forwarding is active
-   To stop port-forwarding at any time, you can press  `Ctrl+C`  in the terminal where it's running

1.  Start by creating a deployment with one replica and an Nginx container:
    
    ```bash
    kubectl create deployment nginx --image=nginx --replicas=1
    ```
    
    This command creates a deployment running the official Nginx container image.
    
2.  Wait for the pod to become ready:
    
    ```bash
    kubectl wait --for=condition=Ready pod -l app=nginx
    ```
    
    Get the pod name that we'll use for port forwarding:
    
    ```bash
    kubectl get pod -l app=nginx
    ```
    
    You should see output similar to:
    
    ```
    NAME                     READY   STATUS    RESTARTS   AGE
    nginx-66b6c48dd5-abcd1   1/1     Running   0          30s
    ```
    
3.  Use the  `kubectl port-forward`  command to forward a local port to the pod:
    
    First, get your pod name:
    
    ```bash
    export POD_NAME=$(kubectl get pods -l app=nginx -o jsonpath='{.items[0].metadata.name}')
    echo $POD_NAME
    ```
    
    You should see output like:
    
    ```
    nginx-748c667d99-pdhzs
    ```
    
    Now use the pod name to set up port forwarding:
    
    ```bash
    kubectl port-forward $POD_NAME 19000:80
    ```
    
    You should see output like:
    
    ```
    Forwarding from 127.0.0.1:19000 -> 80
    Forwarding from [::1]:19000 -> 80
    ```
    
4.  Open a new terminal window (since port-forward keeps running in the current terminal) and verify the port forwarding:
    
    ```bash
    curl http://localhost:19000
    ```
    
    You should see the Nginx welcome page HTML content.
    
    You can also open a web browser and visit  `http://localhost:19000`  to see the page rendered.

### Forwarding Multiple Local Ports to a Pod

Before starting this step, you need to:

1.  Stop the port-forward from Step 1, go back to that terminal and press  `Ctrl+C`

In this step, you will learn how to forward multiple local ports to a pod. We'll forward two different local ports to the same container port, which is useful when you want to provide different access points to the same service.

1.  Use the following commands to set up port forwarding:
    
    First, get your pod name if you haven't already:
    
    ```bash
    export POD_NAME=$(kubectl get pods -l app=nginx -o jsonpath='{.items[0].metadata.name}')
    echo $POD_NAME
    ```
    
    You should see output like:
    
    ```
    nginx-748c667d99-pdhzs
    ```
    
    Now set up port forwarding to map two local ports (19080 and 19081) to the container's port 80:
    
    ```bash
    # The correct format is: kubectl port-forward POD_NAME LOCAL_PORT:CONTAINER_PORT [LOCAL_PORT:CONTAINER_PORT ...]
    kubectl port-forward pod/$POD_NAME 19080:80 19081:80
    ```
    
    You should see output like:
    
    ```
    Forwarding from 127.0.0.1:19080 -> 80
    Forwarding from [::1]:19080 -> 80
    Forwarding from 127.0.0.1:19081 -> 80
    Forwarding from [::1]:19081 -> 80
    ```
    
    This command forwards:
    
    -   Local port 19080 to container port 80
    -   Local port 19081 to container port 80
    
    Both local ports are mapped to the same Nginx container port 80, allowing you to access the same web server through different local ports.
    
2.  Verify the port forwarding by checking the listening ports:
    
    ```bash
    ss -tulnp | grep 1908
    ```
    
    You should see output similar to this:
    
    ```
    tcp   LISTEN  0       4096         0.0.0.0:19080     0.0.0.0:*
    tcp   LISTEN  0       4096         0.0.0.0:19081     0.0.0.0:*
    ```
    
3.  Now you can access the Nginx welcome page through either port:
   
	   ```bash
	    curl http://localhost:19080
	    curl http://localhost:19081
	   ```

Both URLs will show the same Nginx welcome page since they're both forwarded to the same container port.

### Forwarding a Local Port to a Pod with Multiple Containers

Before starting this step:

1.  If you have any port-forward commands running from previous steps, go to those terminals and press  `Ctrl+C`  to stop them
2.  We'll start fresh with a new multi-container pod setup

In this step, you will learn how to forward a local port to a specific container in a pod with multiple containers. This is a common scenario in microservices architectures where sidecars are used.

1.  First, let's clean up the previous resources:
    
    ```bash
    kubectl delete deployment nginx
    ```
    
2.  Create a pod with two containers: Nginx and BusyBox:
    
    ```bash
    cat << EOF | kubectl apply -f -
    apiVersion: v1
    kind: Pod
    metadata:
      name: nginx-busybox
      labels:
        app: nginx-multi
    spec:
      containers:
      - name: nginx
        image: nginx
        ports:
        - containerPort: 80
      - name: busybox
        image: busybox
        command: ['sh', '-c', 'while true; do sleep 3600; done']
    EOF
    ```
    
3.  Wait for the pod to become ready:
    
    ```bash
    kubectl wait --for=condition=Ready pod/nginx-busybox
    ```
    
4.  Verify both containers are running:
    
    ```bash
    kubectl get pod nginx-busybox -o wide
    ```
    
    You should see  `2/2`  under the  `READY`  column.
    
5.  Use the  `kubectl port-forward`  command to forward a local port to the Nginx container:
    
    ```bash
    kubectl port-forward pod/nginx-busybox 19001:80
    ```
    
6.  In a new terminal, verify the connection:
    
    ```bash
    curl http://localhost:19001
    ```
    
    You should see the Nginx welcome page HTML content.

### Using Port-Forward with Kubernetes Services - *Important*

Before starting this step:

1.  If you have any port-forward commands running from Step 3, go to that terminal and press  `Ctrl+C`  to stop it
2.  Keep in mind that we'll create a new deployment and service in this step, but you don't need to delete the previous pod as it won't interfere with our new resources

In this step, you will learn how to use the  `kubectl port-forward`  command with Kubernetes services. Port forwarding to a service is different from port forwarding to a pod because it lets you access any pod that the service points to.

1.  First, create a new deployment with multiple replicas:
    
    ```bash
    kubectl create deployment nginx-service --image=nginx --replicas=3
    ```
    
2.  Wait for all pods to be ready:
    
    ```bash
    kubectl wait --for=condition=Ready pod -l app=nginx-service
    ```
    
3.  Create a service for the deployment:
    
    ```bash
    kubectl expose deployment nginx-service --port=80 --type=ClusterIP --name=nginx-service
    ```
    
4.  Verify the service is created:
    
    ```bash
    kubectl get service nginx-service
    ```
    
    You should see output like:
    
    ```
    NAME           TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)   AGE
    nginx-service  ClusterIP   10.96.123.456   <none>        80/TCP    10s
    ```
    
5.  Use the  `kubectl port-forward`  command to forward a local port to the service:
    
    ```bash
    kubectl port-forward service/nginx-service 20000:80
    ```
    
6.  In a new terminal, test the connection:
    
    ```bash
    curl http://localhost:20000
    ```
    
    You should see the Nginx welcome page HTML content. Try running this command multiple times - you might notice responses coming from different pods behind the service.