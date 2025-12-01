### Deploy a Sample Application

In this step, you'll learn how to create and deploy a Kubernetes application using a YAML manifest. We'll create a simple NGINX web server deployment to demonstrate the process of defining and applying Kubernetes resources.

Create an YAML file with the following content:

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
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

Verify the deployment and pods:

```bash
kubectl get deployments
kubectl get pods
```

Example output for  `kubectl get deployments`:

```
NAME               READY   UP-TO-DATE   AVAILABLE   AGE
nginx-deployment   3/3     3            3           1m
```

Example output for  `kubectl get pods`:

```
NAME                                READY   STATUS    RESTARTS   AGE
nginx-deployment-xxx-yyy            1/1     Running   0          1m
nginx-deployment-xxx-zzz            1/1     Running   0          1m
nginx-deployment-xxx-www            1/1     Running   0          1m
```

Wait for the pods to be in the "Running" state before proceeding.

Let's break down the YAML manifest:

-   `apiVersion`: Specifies the Kubernetes API version
-   `kind`: Defines the resource type (Deployment)
-   `metadata`: Provides name and labels for the deployment
-   `spec.replicas`: Sets the number of pod replicas
-   `selector`: Helps the deployment manage the correct pods
-   `template`: Defines the pod specification
-   `containers`: Specifies the container image and port

This deployment creates three identical NGINX pods, demonstrating how Kubernetes manages containerized applications.

### Create a Service via YAML to Expose the Application Internally or Externally

In this step, you'll learn how to create Kubernetes Services to expose your NGINX deployment internally and externally. We'll demonstrate two common service types: ClusterIP and NodePort.

Create a ClusterIP service YAML file:

```bash
nano nginx-clusterip-service.yaml
```

Add the following service manifest:

```yml
apiVersion: v1
kind: Service
metadata:
  name: nginx-clusterip-service
spec:
  selector:
    app: nginx
  type: ClusterIP
  ports:
    - port: 80
      targetPort: 80
```

Now, create a NodePort service YAML file:

```bash
nano nginx-nodeport-service.yaml
```

Add the following service manifest:

```yml
apiVersion: v1
kind: Service
metadata:
  name: nginx-nodeport-service
spec:
  selector:
    app: nginx
  type: NodePort
  ports:
    - port: 80
      targetPort: 80
      nodePort: 30080
```

Apply both service configurations:

```bash
kubectl apply -f nginx-clusterip-service.yaml
kubectl apply -f nginx-nodeport-service.yaml
```

Example output:

```
service/nginx-clusterip-service created
service/nginx-nodeport-service created
```

Verify the services:

```bash
kubectl get services
```

Example output:

```
NAME                        TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
kubernetes                  ClusterIP   10.96.0.1        <none>        443/TCP        30m
nginx-clusterip-service     ClusterIP   10.104.xxx.xxx   <none>        80/TCP         1m
nginx-nodeport-service      NodePort    10.108.yyy.yyy   <none>        80:30080/TCP   1m
```

To access the NodePort service, get the Minikube IP:

```bash
minikube ip
```

Example output:

```
192.168.49.2
```

Key differences between service types:

-   ClusterIP: Internal cluster access only
-   NodePort: Exposes service on a static port on each node's IP
-   NodePort range: 30000-32767

```bash
curl $(minikube ip):30080
<html>
	<body>
		<h1>Welcome to nginx!</h1>
	</body>
</html>
```

### Verify Service Configuration

In this step, you'll learn how to use  `kubectl describe service`  to inspect the detailed configuration of Kubernetes services and understand their network properties.

Describe the ClusterIP service in detail:

```bash
kubectl describe service nginx-clusterip-service
```

Example output:

```
Name:              nginx-clusterip-service
Namespace:         default
Labels:            <none>
Annotations:       <none>
Selector:          app=nginx
Type:              ClusterIP
IP Family Policy:  SingleStack
IP Families:       IPv4
IP:                10.104.xxx.xxx
IPs:               10.104.xxx.xxx
Port:              <unset>  80/TCP
TargetPort:        80/TCP
Endpoints:         10.244.0.7:80,10.244.0.8:80,10.244.0.9:80
Session Affinity:  None
Events:            <none>
```

Now, describe the NodePort service:

```bash
kubectl describe service nginx-nodeport-service
```

Example output:

```
Name:                     nginx-nodeport-service
Namespace:                default
Labels:                   <none>
Annotations:              <none>
Selector:                 app=nginx
Type:                     NodePort
IP Family Policy:         SingleStack
IP Families:              IPv4
IP:                       10.108.yyy.yyy
IPs:                      10.108.yyy.yyy
Port:                     <unset>  80/TCP
TargetPort:               80/TCP
NodePort:                 <unset>  30080/TCP
Endpoints:                10.244.0.7:80,10.244.0.8:80,10.244.0.9:80
Session Affinity:         None
External Traffic Policy:  Cluster
Events:                   <none>
```

Inspect the service endpoints to verify network connectivity:

```bash
kubectl get endpoints
```

Example output:

```
NAME                        ENDPOINTS                               AGE
kubernetes                  192.168.49.2:8443                       45m
nginx-clusterip-service     10.244.0.7:80,10.244.0.8:80,10.244.0.9:80   15m
nginx-nodeport-service      10.244.0.7:80,10.244.0.8:80,10.244.0.9:80   15m
```

Key information to understand from the service description:

-   **Selector**: Shows which pods are part of the service
-   **IP**: Cluster-internal IP address of the service
-   **Endpoints**: List of pod IP addresses and ports serving the service
-   **Port and TargetPort**: Define how traffic is routed
-   **NodePort**: External port for NodePort service type

### Use Labels to Organize and Select Resources

In this step, you'll learn how to use labels in Kubernetes to organize and select resources efficiently. Labels are key-value pairs that help you manage and organize Kubernetes objects.

First, verify the current labels on your pods:

```bash
kubectl get pods --show-labels
```

Example output:

```
NAME                                READY   STATUS    RESTARTS   AGE   LABELS
nginx-deployment-xxx-yyy            1/1     Running   0          30m   app=nginx,pod-template-hash=xxx
nginx-deployment-xxx-zzz            1/1     Running   0          30m   app=nginx,pod-template-hash=yyy
nginx-deployment-xxx-www            1/1     Running   0          30m   app=nginx,pod-template-hash=zzz
```

Select pods using specific labels:

```bash
kubectl get pods -l app=nginx
```

Example output:

```
NAME                                READY   STATUS    RESTARTS   AGE
nginx-deployment-xxx-yyy            1/1     Running   0          30m
nginx-deployment-xxx-zzz            1/1     Running   0          30m
nginx-deployment-xxx-www            1/1     Running   0          30m
```

Let's add a custom label to one of the pods:

```bash
kubectl label pods nginx-deployment-xxx-yyy environment=development
```

Replace  `nginx-deployment-xxx-yyy`  with the name of one of your pods.

Example output:

```
pod/nginx-deployment-xxx-yyy labeled
```

Now, select pods with multiple label selectors:

```bash
kubectl get pods -l app=nginx,environment=development
```

Example output:

```
NAME                                READY   STATUS    RESTARTS   AGE
nginx-deployment-xxx-yyy            1/1     Running   0          30m
```

Remove a label from a pod:

```bash
kubectl label pods nginx-deployment-xxx-yyy environment-
```

Example output:

```
pod/nginx-deployment-xxx-yyy unlabeled
```

Demonstrate label selection in services:

```bash
kubectl describe service nginx-clusterip-service
```

Look for the "Selector" section, which shows how services use labels to identify pods.

Key points about labels:

-   Labels are key-value pairs attached to Kubernetes objects
-   Used for organizing, selecting, and filtering resources
-   Can be added, modified, or removed dynamically
-   Services and deployments use labels to manage related pods

### Delete and Manage Services

In this step, you'll learn how to delete and manage Kubernetes services using kubectl commands. Understanding service management is crucial for maintaining and cleaning up your Kubernetes resources.

First, list the current services:

```bash
kubectl get services
```

Example output:

```
NAME                        TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
kubernetes                  ClusterIP   10.96.0.1        <none>        443/TCP        1h
nginx-clusterip-service     ClusterIP   10.104.xxx.xxx   <none>        80/TCP         45m
nginx-nodeport-service      NodePort    10.108.yyy.yyy   <none>        80:30080/TCP   45m
```

Delete a specific service using kubectl delete:

```bash
kubectl delete service nginx-clusterip-service
```

Example output:

```
service "nginx-clusterip-service" deleted
```

Verify the service deletion:

```bash
kubectl get services
```

Example output:

```
NAME                        TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
kubernetes                  ClusterIP   10.96.0.1        <none>        443/TCP        1h
nginx-nodeport-service      NodePort    10.108.yyy.yyy   <none>        80:30080/TCP   45m
```

To demonstrate multiple service deletion more clearly, let's recreate our services and then delete them together:

```bash
# Recreate services
kubectl apply -f nginx-clusterip-service.yaml
kubectl apply -f nginx-nodeport-service.yaml
```

```bash
# Delete multiple services at once
kubectl delete service nginx-clusterip-service nginx-nodeport-service
```

Example output:

```
service "nginx-clusterip-service" deleted
service "nginx-nodeport-service" deleted
```

Verify all services are removed (except the default kubernetes service):

```bash
kubectl get services
```

Example output:

```
NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   1h
```

Key points about service deletion:

-   Deleting a service removes the network endpoint
-   Pods are not deleted when a service is removed
-   Default kubernetes service cannot be deleted
-   You can delete services using name, YAML file, or labels
-   Multiple services can be deleted simultaneously by listing them or using label selectors

### Introduce Ingress Basics and Show a Simple Ingress YAML Example

In this step, you'll learn about Kubernetes Ingress, a powerful way to manage external access to services in a Kubernetes cluster.

### What is Ingress?

Ingress is an API object that manages external access to services in a Kubernetes cluster, typically HTTP. Ingress provides:

-   **Load balancing**: Distributes traffic to multiple backend services
-   **SSL/TLS termination**: Handles secure connections
-   **Name-based virtual hosting**: Routes requests to different services based on the hostname
-   **Path-based routing**: Routes requests to different services based on the URL path

Ingress consists of two components:

1.  **Ingress Resource**: A Kubernetes API object that defines the routing rules
2.  **Ingress Controller**: The implementation that enforces the rules defined in the Ingress Resource

Let's enable the Ingress addon in Minikube:

```bash
minikube addons enable ingress
```

Example output:

```
💡  ingress is an addon maintained by Kubernetes. For any concerns contact minikube on GitHub.
🔉  ingress was successfully enabled
```

Create a deployment for two sample applications:

```bash
kubectl create deployment web1 --image=nginx:alpine
kubectl create deployment web2 --image=httpd:alpine
```

Expose these deployments as services:

```bash
kubectl expose deployment web1 --port=80 --type=ClusterIP --name=web1-service
kubectl expose deployment web2 --port=80 --type=ClusterIP --name=web2-service
```

Create an Ingress YAML file:

```bash
nano ingress-example.yaml
```

Add the following Ingress configuration:

```yml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: example-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
    - http:
        paths:
          - path: /web1
            pathType: Prefix
            backend:
              service:
                name: web1-service
                port:
                  number: 80
          - path: /web2
            pathType: Prefix
            backend:
              service:
                name: web2-service
                port:
                  number: 80
```

Key components of this Ingress configuration:

-   **metadata.annotations**: Specific configurations for the Ingress controller
-   **spec.rules**: Define how traffic is routed to services
-   **path**: URL path that will be matched
-   **pathType**: How the path should be matched (Prefix, Exact, or ImplementationSpecific)
-   **backend.service**: The service and port to route traffic to

Apply the Ingress configuration:

```bash
kubectl apply -f ingress-example.yaml
```

Verify the Ingress resource:

```bash
kubectl get ingress
```

Example output:

```
NAME              CLASS   HOSTS   ADDRESS        PORTS   AGE
example-ingress   nginx   *       192.168.49.2   80      1m
```

Check Ingress details:

```bash
kubectl describe ingress example-ingress
```

Example output will show routing rules and backend services.

Testing the Ingress:

```sh
# Get the Minikube IP
minikube ip

# Test access to the services through Ingress
curl $(minikube ip)/web1
curl $(minikube ip)/web2
```

Each command should return the default page from the respective web server.

In production environments, Ingress can be configured with:

-   Multiple hostname-based rules
-   TLS certificates for HTTPS
-   Authentication mechanisms
-   Rate limiting
-   Custom timeout configurations
-   Session affinity
-   And many more advanced features

For more comprehensive coverage of Ingress, refer to the Kubernetes documentation and consider exploring dedicated Ingress controller documentation like NGINX Ingress or Traefik.
