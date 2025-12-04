### Explore the  `kubectl run`  Command

The  `kubectl run`  command is used to create and run a specific image in a pod. It provides multiple options to customize the pod's behavior, environment, and specifications.

Run the following command to view the available options for  `kubectl run`:

```bash
kubectl run -h
```

You will see the following output:

```plaintext
Create and run a particular image in a pod.

Examples:
# Start a nginx pod
kubectl run nginx --image=nginx

# Start a hazelcast pod and let the container expose port 5701
kubectl run hazelcast --image=hazelcast/hazelcast --port=5701

# Start a hazelcast pod and set environment variables "DNS_DOMAIN=cluster" and "POD_NAMESPACE=default" in the container
kubectl run hazelcast --image=hazelcast/hazelcast --env="DNS_DOMAIN=cluster" --env="POD_NAMESPACE=default"

# Start a hazelcast pod and set labels "app=hazelcast" and "env=prod" in the container
kubectl run hazelcast --image=hazelcast/hazelcast --labels="app=hazelcast,env=prod"

# Dry run; print the corresponding API objects without creating them
kubectl run nginx --image=nginx --dry-run=client

# Start a nginx pod, but overload the spec with a partial set of values parsed from JSON
kubectl run nginx --image=nginx --overrides='{ "apiVersion": "v1", "spec": { ... } }'

# Start a busybox pod and keep it in the foreground, don't restart it if it exits
kubectl run -i -t busybox --image=busybox --restart=Never

# Start the nginx pod using the default command, but use custom arguments (arg1 .. argN) for that command
kubectl run nginx --image=nginx -- <arg1> <arg2> ... <argN>

# Start the nginx pod using a different command and custom arguments
kubectl run nginx --image=nginx --command -- <cmd> <arg1> ... <argN>
```

### Create a Pod

A  **pod**  is the smallest deployable unit in Kubernetes and represents one or more containers running together. In this step, we will create a pod running an Nginx web server.

1.  **Create the pod**:
    
    Run the following command to create a pod named  `nginx-pod`:
    
    ```bash
    kubectl run nginx-pod --image=nginx
    ```
    
    -   The  `--image`  option specifies the container image to use. Here, we use the official Nginx image.
2.  **Verify the pod**:
    
    Check that the pod is running:
    
    ```bash
    kubectl get pods
    ```
    
    -   Look for  `nginx-pod`  in the output.
    -   The  `STATUS`  column should show  `Running`  when the pod is ready.

If the pod status shows  `Pending`, Kubernetes may still be pulling the container image. Wait a few moments and rerun  `kubectl get pods`.

### Create a Deployment and Scale Replicas

A  **deployment**  manages a set of pods and ensures they are running as desired. It is useful for scaling and updating applications.

1.  **Create the deployment**:
    
    Run the following command to create a deployment named  `nginx-deployment`:
    
    ```bash
    kubectl create deployment nginx-deployment --image=nginx
    ```
    
    -   The  `--image`  option specifies the container image to use.
2.  **Scale the deployment to 3 replicas**:
    
    Since the  `--replicas`  flag is deprecated, we will scale the deployment using  `kubectl scale`  instead.
    
    Use the  `kubectl scale`  command to adjust the number of replicas:
    
    ```bash
    kubectl scale deployment nginx-deployment --replicas=3
    ```
    
    -   This ensures that three pods are running as part of the deployment.
3.  **Verify the deployment and its replicas**:
    
    Check the status of the deployment and pods:
    
    ```bash
    kubectl get deployments
    kubectl get pods
    ```
    
    -   Ensure the deployment shows 3 replicas under the  `READY`  column.
    -   Verify that three pods are listed in the output of  `kubectl get pods`.

If a pod is not in the  `Running`  state, it might be due to insufficient cluster resources. Check the pod events with:

```bash
kubectl describe pod <pod-name>
```
### Create a Job

A  **job**  is used to run tasks that need to complete successfully. For example, batch jobs or data processing tasks. We will use  `kubectl run`  to create a job and verify its execution.

1.  **Create the job**

Run the following command to create a job named  `busybox-job`:

```bash
kubectl run busybox-job --image=busybox --restart=OnFailure -- echo "Hello from Kubernetes"
```

-   The  `--restart=OnFailure`  flag specifies that this is a job.
-   The  `echo`  command defines the task the job will execute.

2.  **Check the job status**

Run the following command to verify the job:

```bash
kubectl get jobs
```

Expected output:

```
NAME          COMPLETIONS   DURATION   AGE
busybox-job   1/1           5s         10s
```

-   `COMPLETIONS`: Shows the job ran successfully once (`1/1`).
-   If no job is listed, it may have been automatically cleaned up. Proceed to the next step to check its pod.

3.  **Verify the job’s pod**

Since a job runs inside a pod, use the following command to verify the pod:

```bash
kubectl get pods
```

Expected output:

```
NAME               READY   STATUS      RESTARTS   AGE
busybox-job        0/1     Completed   0          30s
```

-   The  `STATUS`  field should display  `Completed`, indicating the job has finished.

4.  **Check job output**

Inspect the logs of the job's pod to verify the output:

```bash
kubectl logs busybox-job
```

Expected output:

```
Hello from Kubernetes
```

This confirms the job executed successfully.

### Cleanup

To keep your cluster clean, delete the resources you created during the lab.

1.  **Delete the resources**:
    
    Run the following commands:
    
    ```bash
    kubectl delete pod nginx-pod
    kubectl delete pod busybox-job
    kubectl delete deployment nginx-deployment
    ```
    
2.  **Verify the cleanup**:
    
    Check that no resources remain:
    
    ```bash
    kubectl get pods
    kubectl get deployments
    ```
    
    -   Ensure the output does not list the resources you created.
