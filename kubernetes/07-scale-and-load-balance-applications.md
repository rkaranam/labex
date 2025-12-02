### Deploy a Sample Application

In this step, you'll learn how to deploy a simple web application using a Kubernetes Deployment with a single replica. We'll create a YAML manifest for an NGINX web server and apply it to the Minikube cluster. Understanding how to deploy an application is fundamental to using Kubernetes.

Create a new YAML file for the deployment:

```bash
nano nginx-deployment.yaml
```

Add the following deployment configuration:

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        - name: nginx
          image: nginx:latest
          ports:
            - containerPort: 80
```

Save the file (Ctrl+X, then Y, then Enter).

**Explanation of the YAML configuration:**

-   **`apiVersion: apps/v1`**: Specifies the API version for Deployments.
-   **`kind: Deployment`**: Indicates that this is a Deployment object, used to manage replicated applications.
-   **`metadata`**: Contains metadata about the Deployment.
    -   **`name: nginx-deployment`**: The name of the deployment.
    -   **`labels: app: nginx`**: A label used to identify this deployment.
-   **`spec`**: Contains the deployment specification.
    -   **`replicas: 1`**: The desired number of pod instances (replicas). In this initial deployment, we have only one replica.
    -   **`selector`**: Defines how the deployment will select pods to manage.
        -   **`matchLabels: app: nginx`**: Pods with the label  `app: nginx`  will be managed by this deployment.
    -   **`template`**: The pod template. It specifies the configuration for the pods the deployment creates.
        -   **`metadata.labels: app: nginx`**: Label that applies to pods managed by this deployment
        -   **`spec.containers`**: Defines the containers in the pod
            -   **`name: nginx`**: The name of the container.
            -   **`image: nginx:latest`**: The Docker image for the container (using the latest NGINX image).
            -   **`ports: containerPort: 80`**: Exposes port 80 in the container.

Apply the deployment to the Kubernetes cluster:

```bash
kubectl apply -f nginx-deployment.yaml
```

Example output:

```
deployment.apps/nginx-deployment created
```

Verify the deployment status:

```bash
kubectl get deployments
kubectl get pods
```

Example output for  `kubectl get deployments`:

```
NAME               READY   UP-TO-DATE   AVAILABLE   AGE
nginx-deployment   1/1     1            1           30s
```

Example output for  `kubectl get pods`:

```
NAME                                READY   STATUS    RESTARTS   AGE
nginx-deployment-xxx-yyy            1/1     Running   0          30s
```

Key points about this deployment:

1.  We created a Deployment with a single replica.
2.  The deployment uses the latest NGINX image.
3.  The container exposes port 80.
4.  The deployment has a label  `app: nginx`  for identification.

Inspect the deployment details:

```bash
kubectl describe deployment nginx-deployment
```

Example output will show deployment configuration, events, and current state.

### Scale Deployments to Handle Increased Load

In this step, you will learn how to scale your application to handle more traffic. In the real world, as your application becomes more popular, one replica may not be sufficient to handle the load. To address this, Kubernetes allows you to easily scale out your application by increasing the number of pod instances (replicas).

Before scaling, let's briefly discuss why multiple replicas are necessary. A single replica of an application can only handle a certain amount of concurrent requests. If the traffic increases beyond that capacity, the application can become slow or unresponsive. By having multiple replicas, the load can be distributed across different pod instances, ensuring that the application remains responsive and available. This concept is essential for creating scalable applications.

You will now learn how to scale your Kubernetes deployment by modifying the  `replicas`  field in the YAML manifest, and also by using the  `kubectl scale`  command.

Open the previously created deployment manifest:

```bash
nano ~/project/k8s-manifests/nginx-deployment.yaml
```

Modify the  `replicas`  field from 1 to 3:

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3 # Changed from 1 to 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        - name: nginx
          image: nginx:latest
          ports:
            - containerPort: 80
```

Save the file (Ctrl+X, then Y, then Enter).

Apply the updated deployment:

```bash
kubectl apply -f ~/project/k8s-manifests/nginx-deployment.yaml
```

Example output:

```
deployment.apps/nginx-deployment configured
```

Verify the scaled deployment:

```bash
kubectl get deployments
kubectl get pods
```

Example output for  `kubectl get deployments`:

```
NAME               READY   UP-TO-DATE   AVAILABLE   AGE
nginx-deployment   3/3     3            3           5m
```

Example output for  `kubectl get pods`:

```
NAME                                READY   STATUS    RESTARTS   AGE
nginx-deployment-xxx-yyy            1/1     Running   0          5m
nginx-deployment-xxx-zzz            1/1     Running   0          30s
nginx-deployment-xxx-www            1/1     Running   0          30s
```

Alternative scaling method using  `kubectl scale`:

```bash
kubectl scale deployment nginx-deployment --replicas=4
```

Example output:

```
deployment.apps/nginx-deployment scaled
```

Verify the new number of replicas:

```bash
kubectl get deployments
kubectl get pods
```

Key points about scaling:

1.  Modify  `replicas`  in the YAML file or use the  `kubectl scale`  command.
2.  Use  `kubectl apply`  to update the deployment when making changes to the YAML file.
3.  Kubernetes ensures the desired number of replicas are running.
4.  You can scale both up (increase replicas) or down (decrease replicas).

### Verify Load Balancing by Checking Multiple Pod Responses

In this step, you'll learn how to verify load balancing in Kubernetes by creating a Service and checking responses from multiple pods. Load balancing is crucial for distributing traffic across multiple replicas, ensuring that no single pod is overwhelmed. Kubernetes Services handle this process automatically.

Create a service to expose the deployment:

```bash
nano ~/project/k8s-manifests/nginx-service.yaml
```

Add the following service configuration:

```yml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx
  type: ClusterIP
  ports:
    - port: 80
      targetPort: 80
```

Save the file (Ctrl+X, then Y, then Enter).

**Explanation of the YAML configuration:**

-   **`apiVersion: v1`**: Specifies the API version for Services.
-   **`kind: Service`**: Indicates that this is a Service object.
-   **`metadata`**: Contains metadata about the Service.
    -   **`name: nginx-service`**: The name of the Service.
-   **`spec`**: Contains the service specification.
    -   **`selector`**: Defines which pods this service will route traffic to.
        -   **`app: nginx`**: Selects pods with the label  `app: nginx`, which matches the pods created in the previous step.
    -   **`type: ClusterIP`**: Creates an internal service with a cluster IP address, used for internal communication. This service type is only reachable within the Kubernetes cluster.
    -   **`ports`**: Defines how the service will map traffic.
        -   **`port: 80`**: The port that the service exposes.
        -   **`targetPort: 80`**: The port that the application inside the container is listening on.

Apply the service:

```bash
kubectl apply -f ~/project/k8s-manifests/nginx-service.yaml
```

Example output:

```
service/nginx-service created
```

Verify the service:

```bash
kubectl get services
```

Example output:

```
NAME            TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
kubernetes      ClusterIP   10.96.0.1       <none>        443/TCP   30m
nginx-service   ClusterIP   10.96.xxx.xxx   <none>        80/TCP    30s
```

Now, to truly verify load balancing, you will create a temporary pod and send multiple requests to the service. This allows you to see that the requests are being distributed across different NGINX pods.

Create a temporary pod to test load balancing:

```bash
kubectl run curl-test --image=curlimages/curl --rm -it -- sh
```

This command does the following:

-   `kubectl run curl-test`: Creates a new pod named  `curl-test`.
-   `--image=curlimages/curl`: Uses a Docker image with  `curl`  installed.
-   `--rm`: Automatically removes the pod when it is finished.
-   `-it`: Allocates a pseudo-TTY and keeps stdin open.
-   `-- sh`: Starts a shell session in the pod.

Inside the temporary pod, run multiple requests:

```bash
for i in $(seq 1 10); do curl -s nginx-service | grep -q "Welcome to nginx!" && echo "Welcome to nginx - Request $i"; done
```

This loop will send 10 requests to the  `nginx-service`. Each request should be routed to one of the available NGINX pods. The output will print  `Welcome to nginx - Request $i`  for each successful request.

Example output:

```
Welcome to nginx - Request 1
Welcome to nginx - Request 2
Welcome to nginx - Request 3
...
```

Exit the temporary pod:

```bash
exit
```

Key points about load balancing:

1.  Services distribute traffic across all matching pods.
2.  Each request can potentially hit a different pod.
3.  Kubernetes uses a round-robin approach by default.
4.  The  `ClusterIP`  service type provides internal load balancing.
5.  The curl test shows the load being distributed across multiple NGINX instances.

### Dynamically Adjust the Deployment Scale to Meet Demand

In this step, you will practice dynamically adjusting your Kubernetes deployment scale to meet changing application demands using the  `kubectl scale`  command. This step emphasizes the practical aspect of adjusting the number of running replicas without directly modifying the YAML file, which can be useful for rapid adjustments in response to traffic spikes.

First, check the current deployment status:

```bash
kubectl get deployments
```

Example output:

```
NAME               READY   UP-TO-DATE   AVAILABLE   AGE
nginx-deployment   4/4     4            4           45m
```

Scale the deployment using the kubectl command:

```bash
kubectl scale deployment nginx-deployment --replicas=5
```

Example output:

```
deployment.apps/nginx-deployment scaled
```

Verify the new number of replicas:

```bash
kubectl get deployments
kubectl get pods
```

Example output for deployments:

```
NAME               READY   UP-TO-DATE   AVAILABLE   AGE
nginx-deployment   5/5     5            5           46m
```

Example output for pods:

```
NAME                                READY   STATUS    RESTARTS   AGE
nginx-deployment-xxx-yyy            1/1     Running   0          1m
nginx-deployment-xxx-zzz            1/1     Running   0          1m
nginx-deployment-xxx-www            1/1     Running   0          1m
nginx-deployment-xxx-aaa            1/1     Running   0          1m
nginx-deployment-xxx-bbb            1/1     Running   0          1m
```

Now, update the deployment YAML for persistent scaling:

```bash
nano ~/project/k8s-manifests/nginx-deployment.yaml
```

Modify the  `replicas`  field:

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 5 # Updated from previous value
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        - name: nginx
          image: nginx:latest
          ports:
            - containerPort: 80
```

Apply the updated configuration:

```bash
kubectl apply -f ~/project/k8s-manifests/nginx-deployment.yaml
```

Example output:

```
deployment.apps/nginx-deployment configured
```

Simulate scaling down for reduced demand:

```bash
kubectl scale deployment nginx-deployment --replicas=2
```

Example output:

```
deployment.apps/nginx-deployment scaled
```

Verify the reduced number of replicas:

```bash
kubectl get deployments
kubectl get pods
```

Key points about scaling:

1.  Use  `kubectl scale`  for quick, temporary scaling.
2.  Update YAML for persistent configuration.
3.  Kubernetes ensures smooth scaling with minimal disruption.
4.  You can scale up or down based on application needs using both command and configuration.

### Monitor Deployment and Pod Events for Changes

In this step, you'll learn how to monitor Kubernetes deployments and pods using various kubectl commands to track changes, troubleshoot issues, and understand the lifecycle of your applications. Observability is crucial for ensuring the health and performance of your applications.

Describe the current deployment to get detailed information:

```bash
kubectl describe deployment nginx-deployment
```

Example output:

```
Name:                   nginx-deployment
Namespace:              default
CreationTimestamp:      [timestamp]
Labels:                 app=nginx
Replicas:               2 desired | 2 updated | 2 total | 2 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  app=nginx
  Containers:
   nginx:
    Image:        nginx:latest
    Port:         80/TCP
    Host Port:    0/TCP
    Environment:  <none>
    Mounts:       <none>
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Available      True    MinimumReplicasAvailable
  Progressing    True    NewReplicaSetAvailable
OldReplicaSets:  <none>
NewReplicaSet:   nginx-deployment-xxx (2/2 replicas created)
Events:          <some deployment events>
```

Get detailed information about individual pods:

```bash
kubectl describe pods -l app=nginx
```

Example output will show details for each pod, including:

-   Current status
-   Container information
-   Events
-   IP addresses
-   Node information

View cluster-wide events:

```bash
kubectl get events
```

Example output:

```
LAST SEEN   TYPE      REASON              OBJECT                           MESSAGE
5m          Normal    Scheduled           pod/nginx-deployment-xxx-yyy    Successfully assigned default/nginx-deployment-xxx-yyy to minikube
5m          Normal    Pulled              pod/nginx-deployment-xxx-yyy    Container image "nginx:latest" already present on machine
5m          Normal    Created             pod/nginx-deployment-xxx-yyy    Created container nginx
5m          Normal    Started             pod/nginx-deployment-xxx-yyy    Started container nginx
```

Filter events for specific resources:

```bash
kubectl get events --field-selector involvedObject.kind=Deployment
```

Example output will show only deployment-related events.

Simulate an event by deleting a pod:

```bash
# Get a pod name
POD_NAME=$(kubectl get pods -l app=nginx -o jsonpath='{.items[0].metadata.name}')

# Delete the pod
kubectl delete pod $POD_NAME
```

Observe the events and pod recreation:

```bash
kubectl get events
kubectl get pods
```

Key points about monitoring:

1.  `kubectl describe`  provides detailed resource information.
2.  `kubectl get events`  shows cluster-wide events.
3.  Kubernetes automatically replaces deleted pods.
4.  Events help troubleshoot deployment issues.
5.  Use  `describe`  for detailed object information and  `events`  to track actions.

### Briefly Introduce Horizontal Pod Autoscaler (HPA) for Future Learning

In this step, you'll get an introduction to Horizontal Pod Autoscaler (HPA), a powerful Kubernetes feature that automatically scales applications based on resource utilization. HPA allows you to define scaling rules based on metrics like CPU utilization, memory usage, or even custom metrics.

**Understanding HPA:**

HPA automatically adjusts the number of running pod replicas in a Deployment, ReplicaSet, or StatefulSet based on observed CPU or memory usage, or based on custom metrics provided by your applications. This ensures that your application can automatically scale to handle changing traffic loads, improving performance and availability.

Enable metrics server addon in Minikube:

```bash
minikube addons enable metrics-server
```

Example output:

```
* The 'metrics-server' addon is enabled
```

The metrics server provides Kubernetes with usage data about your resources and it is essential for the HPA to function.

Create a deployment with resource requests:

```bash
nano ~/project/k8s-manifests/hpa-example.yaml
```

Add the following content:

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: php-apache
spec:
  selector:
    matchLabels:
      run: php-apache
  replicas: 1
  template:
    metadata:
      labels:
        run: php-apache
    spec:
      containers:
        - name: php-apache
          image: k8s.gcr.io/hpa-example
          ports:
            - containerPort: 80
          resources:
            limits:
              cpu: 500m
            requests:
              cpu: 200m
---
apiVersion: v1
kind: Service
metadata:
  name: php-apache
  labels:
    run: php-apache
spec:
  ports:
    - port: 80
  selector:
    run: php-apache
```

Apply the deployment:

```bash
kubectl apply -f ~/project/k8s-manifests/hpa-example.yaml
```

**Explanation of the YAML configuration:**

-   This YAML file defines a Deployment for a PHP application and the corresponding Service.
-   The Deployment configuration is very similar to the NGINX one, with the exception of:
    -   **`name: php-apache`**: The name of the deployment and pod container.
    -   **`image: k8s.gcr.io/hpa-example`**: The Docker image for the container.
    -   **`resources`**: This section specifies the resource requirements for the container.
        -   **`limits.cpu: 500m`**: The maximum CPU allowed to use by the container.
        -   **`requests.cpu: 200m`**: The guaranteed CPU amount assigned to the container.
-   The service is a standard service configuration, exposing the deployment internally.

Create an HPA configuration:

```bash
nano ~/project/k8s-manifests/php-apache-hpa.yaml
```

Add the following HPA manifest:

```yml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: php-apache
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: php-apache
  minReplicas: 1
  maxReplicas: 10
  metrics:
    - type: Resource
      resource:
        name: cpu
        target:
          type: Utilization
          averageUtilization: 50
```

Apply the HPA configuration:

```bash
kubectl apply -f ~/project/k8s-manifests/php-apache-hpa.yaml
```

**Explanation of the YAML configuration:**

-   **`apiVersion: autoscaling/v2`**: Specifies the API version for HorizontalPodAutoscaler.
-   **`kind: HorizontalPodAutoscaler`**: Indicates that this is an HPA object.
-   **`metadata`**: Contains metadata about the HPA.
    -   **`name: php-apache`**: The name of the HPA.
-   **`spec`**: Contains the HPA specification.
    -   **`scaleTargetRef`**: Defines the target Deployment that will be scaled.
        -   **`apiVersion: apps/v1`**: The API version of the target resource.
        -   **`kind: Deployment`**: The target resource type, which is a Deployment.
        -   **`name: php-apache`**: The name of the target Deployment to scale.
    -   **`minReplicas: 1`**: The minimum number of replicas to keep running.
    -   **`maxReplicas: 10`**: The maximum number of replicas to scale to.
    -   **`metrics`**: Defines how to determine scaling metrics.
        -   **`type: Resource`**: Scales based on a resource metric.
        -   **`resource.name: cpu`**: Scales based on CPU usage.
        -   **`resource.target.type: Utilization`**: Scales based on a percentage of the CPU requested by the pod
        -   **`resource.target.averageUtilization: 50`**: Scales when average CPU usage across all pods exceeds 50% of the requests.

Verify the HPA configuration:

```bash
kubectl get hpa
```

Example output:

```
NAME         REFERENCE              TARGETS         MINPODS   MAXPODS   REPLICAS   AGE
php-apache   Deployment/php-apache  0%/50%          1         10        1          30s
```

### Simulate Load and Observe Autoscaling in Real-time

To simulate high load and trigger the autoscaler, you'll run a load generator in one terminal and monitor the scaling activity in a separate terminal.

First, open a terminal for the load generator:

```bash
kubectl run -i --tty load-generator --rm --image=busybox --restart=Never -- /bin/sh -c "while sleep 0.01; do wget -q -O- http://php-apache; done"
```

DO NOT CLOSE the terminal with the load generator.  **OPEN ANOTHER TERMINAL**  to monitor the scaling activity.

In the second terminal, you can use several commands to observe the autoscaling in real-time:

1.  Monitor HPA status (updates every few seconds):

```bash
kubectl get hpa -w
```

2.  Watch the pods being created as the HPA scales up:

```bash
kubectl get pods -w
```

3.  Track events related to the scaling activity:

```bash
kubectl get events --sort-by='.lastTimestamp' -w
```

You can run any of these commands to observe different aspects of the autoscaling process. For example, watching pods with  `-w`  flag shows you pods being created in real-time as the system scales:

Example output for  `kubectl get pods -w`:

```
NAME                         READY   STATUS    RESTARTS   AGE
php-apache-xxxxxxxxx-xxxxx   1/1     Running   0          2m
load-generator               1/1     Running   0          30s
php-apache-xxxxxxxxx-yyyyy   0/1     Pending   0          0s
php-apache-xxxxxxxxx-yyyyy   0/1     ContainerCreating   0          0s
php-apache-xxxxxxxxx-yyyyy   1/1     Running   0          3s
php-apache-xxxxxxxxx-zzzzz   0/1     Pending   0          0s
php-apache-xxxxxxxxx-zzzzz   0/1     ContainerCreating   0          0s
php-apache-xxxxxxxxx-zzzzz   1/1     Running   0          2s
```

You will see the HPA responding to the increased load by scaling up the number of pods. The metrics update may take a minute or more to reflect changes:

Example output for  `kubectl get hpa -w`:

```
NAME         REFERENCE               TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
php-apache   Deployment/php-apache   0%/50%    1         10        1          30s
php-apache   Deployment/php-apache   68%/50%   1         10        1          90s
php-apache   Deployment/php-apache   68%/50%   1         10        2          90s
php-apache   Deployment/php-apache   79%/50%   1         10        2          2m
php-apache   Deployment/php-apache   79%/50%   1         10        4          2m15s
```

When you're done observing, press  `Ctrl+C`  to stop the monitoring command, and go back to the first terminal and press  `Ctrl+C`  to stop the load generator.

Key points about HPA:

1.  Automatically scales pods based on resource utilization, which improves application resilience.
2.  Can scale based on CPU, memory, or custom metrics.
3.  Defines min and max replica counts, ensuring balanced and efficient scaling.
4.  HPA is a crucial component for maintaining application performance and availability under varying load.
5.  Using the  `-w`  (watch) flag with kubectl commands provides real-time monitoring of cluster changes.