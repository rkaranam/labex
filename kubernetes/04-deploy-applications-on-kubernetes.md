
## Learn Basic kubectl Commands and Syntax

In this step, you will explore fundamental  `kubectl`  commands.  `kubectl`  is the command-line tool that allows you to interact with your Kubernetes cluster. It is essential for managing Kubernetes resources. We will demonstrate how to use  `kubectl`  to view resources and understand basic Kubernetes object management.

Let's start by exploring the namespaces within your Kubernetes cluster. Namespaces are a way to organize your Kubernetes resources. By default, Kubernetes clusters have several namespaces for system components and user resources. To see a list of namespaces, run the following command:

```bash
kubectl get namespaces
```
You will typically see at least these default namespaces:

-   `default`: The default namespace for user-created resources if no other namespace is specified.
-   `kube-node-lease`: Used for node leases, which help the control plane track the health of nodes.
-   `kube-public`: Intended for resources that should be publicly accessible (though this is rarely used for sensitive information).
-   `kube-system`: Contains system-level resources, such as core Kubernetes components.

Next, let's view the system components running in the  `kube-system`  namespace. Many core Kubernetes components run as pods within this namespace. To view pods in a specific namespace, use the  `-n`  or  `--namespace`  flag followed by the namespace name. Run the following command to see pods in the  `kube-system`  namespace:

```bash
kubectl get pods -n kube-system
```

This command will list all the pods running in the  `kube-system`  namespace. Example output:

```
NAME                               READY   STATUS    RESTARTS   AGE
coredns-787d4945fb-j8rhx           1/1     Running   0          15m
etcd-minikube                       1/1     Running   0          15m
kube-apiserver-minikube             1/1     Running   0          15m
kube-controller-manager-minikube    1/1     Running   0          15m
kube-proxy-xb9rz                    1/1     Running   0          15m
kube-scheduler-minikube             1/1     Running   0          15m
storage-provisioner                 1/1     Running   0       	 15m
```

This output shows the names of pods, their `READY` status (how many containers in the pod are ready out of the total), their `STATUS` (e.g., `Running`), how many times they have `RESTARTED`, and their `AGE`. These are the essential components that make Kubernetes work.

Now, let's explore some basic  `kubectl`  command patterns. The general syntax for  `kubectl`  commands is:

```bash
kubectl [command] [TYPE] [NAME] [flags]
```

Let's break this down:

-   `kubectl`: The command-line tool itself.
-   `[command]`: Specifies what action you want to perform. Common commands include:
    -   `get`: Display one or many resources.
    -   `describe`: Show details about a specific resource.
    -   `create`: Create a new resource.
    -   `delete`: Delete resources.
    -   `apply`: Apply a configuration to a resource. We'll use this a lot later.
-   `[TYPE]`: Specifies the Kubernetes resource type you want to interact with. Common resource types include:
    -   `pods`: The smallest deployable units in Kubernetes.
    -   `deployments`: Manage sets of pods for scaling and updates.
    -   `services`: Expose applications running in pods.
    -   `nodes`: The worker machines in your Kubernetes cluster.
    -   `namespaces`: Logical groupings of resources.
-   `[NAME]`: The name of a specific resource. This is optional; if you omit the name,  `kubectl`  will operate on all resources of the specified type.
-   `[flags]`: Optional flags to modify the command's behavior (e.g.,  `-n <namespace>`,  `-o wide`).

Let's see some examples:

```bash
# Get all resources in the default namespace
kubectl get all

# Describe a specific resource type (the 'minikube' node)
kubectl describe nodes minikube
```
The `kubectl get all` command will retrieve information about all resource types (services, deployments, pods, etc.) in the `default` namespace.

## Create a Simple YAML Manifest

Before you create your first YAML manifest, it's important to understand the key Kubernetes objects you'll be working with. These objects are the building blocks for managing and orchestrating your applications within Kubernetes.

### Understanding Kubernetes Objects

-   **Pod**: The most basic unit in Kubernetes. A pod is like a box that can hold one or more containers. These containers within a pod share the same network and storage. Think of a pod as representing a single instance of your application.
-   **Deployment**: Deployments are used to manage pods. They ensure that a desired number of pod replicas are running at all times. If a pod fails, a deployment will automatically replace it. Deployments also handle updates to your application in a controlled way, like rolling updates.
-   **Service**: Services provide a stable way to access your application running in pods. Because pods can be created and destroyed, their IP addresses can change. A service provides a fixed IP address and DNS name that always points to the set of pods it manages. This allows other parts of your application, or external users, to reliably access your application without needing to track individual pod IPs.

Here’s a diagram to illustrate the relationships between these objects:

<img width="312" height="439" alt="relationship-in-k8s-objects" src="https://github.com/user-attachments/assets/ec3bb742-49f8-4450-9116-7df8b663e7f2" />

Understanding these objects is crucial because you will define their desired state and configuration using YAML manifests.

### YAML Manifest Overview

A YAML manifest in Kubernetes is a file written in YAML format that describes the Kubernetes objects you want to create or manage. YAML is a human-readable data serialization language. Using YAML for Kubernetes manifests has several advantages:

1.  **Declarative Management**: You describe the  _desired state_  of your resources in the YAML file (e.g., "I want 3 replicas of my application running"). Kubernetes then works to make the actual state match your desired state. This is called declarative management.
2.  **Version Control**: YAML files are text-based and can be easily stored in version control systems like Git. This allows you to track changes to your Kubernetes configurations over time, rollback to previous configurations, and collaborate with others.
3.  **Reusability and Portability**: You can reuse YAML manifests across different environments (development, testing, production) with minimal changes. This makes your deployments more consistent and reproducible.

Now, you will create your first YAML manifest file. Let's start with a simple manifest for an NGINX pod. NGINX is a popular web server. We will create a pod that runs a single NGINX container. Use the  `nano`  text editor to create a file named  `nginx-pod.yaml`:

```bash
nano nginx-pod.yaml
```

`nano`  is a simple text editor that runs in your terminal. Once  `nano`  opens, paste the following content into the file:

```yml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
  labels:
    app: nginx
spec:
  containers:
    - name: nginx
      image: nginx:latest
      ports:
        - containerPort: 80
```

Let's understand each part of this YAML manifest:

-   **`apiVersion: v1`**: Specifies the Kubernetes API version to use for creating this object.  `v1`  is the core API group and is used for fundamental objects like pods, services, and namespaces.
-   **`kind: Pod`**: Indicates that you are defining a Pod resource.
-   **`metadata:`**: Contains data about the Pod, such as its name and labels.
    -   **`name: nginx-pod`**: Sets the name of the Pod to  `nginx-pod`. This is how you will refer to this pod within Kubernetes.
    -   **`labels:`**: Labels are key-value pairs that are attached to objects. They are used to organize and select subsets of objects. Here, we are adding a label  `app: nginx`  to this pod.
-   **`spec:`**: Describes the desired state of the Pod.
    -   **`containers:`**: A list of containers to be run within the Pod. In this case, we have only one container.
        -   **`- name: nginx`**: Sets the name of the container to  `nginx`.
        -   **`image: nginx:latest`**: Specifies the container image to use.  `nginx:latest`  refers to the latest version of the NGINX Docker image from Docker Hub.
        -   **`ports:`**: A list of ports that this container will expose.
            -   **`- containerPort: 80`**: Specifies that the container will expose port 80. Port 80 is the standard HTTP port.

Now that you have created your  `nginx-pod.yaml`  file, you need to apply it to your Kubernetes cluster to create the pod. Use the  `kubectl apply`  command with the  `-f`  flag, which specifies the file containing the manifest:

```bash
kubectl apply -f nginx-pod.yaml
```

This command sends the manifest to your Kubernetes cluster, and Kubernetes will create the pod as defined. 
To verify that the pod has been created and is running, use the  `kubectl get pods`  command. This will list all pods in the default namespace. You can also use  `kubectl describe pod nginx-pod`  to get detailed information about the  `nginx-pod`. Run these commands:

```bash
kubectl get pods
kubectl describe pod nginx-pod
```

Now, let's create a manifest for a more complex resource: a Deployment. A Deployment will manage a set of pods, ensuring that the desired number of replicas are running. Create a new file named  `nginx-deployment.yaml`  using  `nano`:

```bash
nano nginx-deployment.yaml
```
Paste the following content into  `nginx-deployment.yaml`:

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

Let's highlight the key differences and new parts compared to the  `nginx-pod.yaml`  manifest:

-   **`apiVersion: apps/v1`**: For Deployments, you use the  `apps/v1`  API version, which is part of the  `apps`  API group and handles higher-level application management resources.
-   **`kind: Deployment`**: Indicates that you are defining a Deployment resource.
-   **`spec:`**: The  `spec`  section for a Deployment is more complex because it defines how the Deployment should manage pods.
    -   **`replicas: 3`**: This is new. It specifies that you want 3 replicas (copies) of your pod to be running. The Deployment will ensure that there are always 3 pods matching the criteria defined in the  `template`.
    -   **`selector:`**: A selector is used by the Deployment to identify which pods it should manage.
        -   **`matchLabels:`**: Defines the labels that pods must have to be selected by this Deployment. Here, it selects pods with the label  `app: nginx`.
    -   **`template:`**: The  `template`  defines the pod specification that the Deployment will use to create new pods. It's essentially the same pod definition as in our  `nginx-pod.yaml`  example, including  `metadata.labels`  and  `spec.containers`.  **Important**: The labels defined here in  `template.metadata.labels`  must match the  `selector.matchLabels`  so that the Deployment can manage these pods.


Now, apply this Deployment manifest to your cluster:

```bash
kubectl apply -f nginx-deployment.yaml
```

You should see output like:

```
deployment.apps/nginx-deployment created
```

Verify the Deployment and the pods it created. Use  `kubectl get deployments`  to check the Deployment status and  `kubectl get pods`  to see the pods.
```bash
kubectl get deployments
kubectl get pods
```

Example output:

```
NAME               READY   UP-TO-DATE   AVAILABLE   AGE
nginx-deployment   3/3     3            3           1m

NAME                                READY   STATUS    RESTARTS   AGE
nginx-deployment-xxx-yyy            1/1     Running   0          1m
nginx-deployment-xxx-zzz            1/1     Running   0          1m
nginx-deployment-xxx-www            1/1     Running   0          1m
```

-   `kubectl get deployments`  shows that the  `nginx-deployment`  has  `READY`  3/3,  `UP-TO-DATE`  3, and  `AVAILABLE`  3. This means the Deployment has successfully created and is managing 3 pods, and all of them are ready and available.
-   `kubectl get pods`  now lists three pods with names starting with  `nginx-deployment-`. These are the pods created and managed by your  `nginx-deployment`.

### Apply the YAML Manifest

In this step, you will explore the  `kubectl apply`  command in more detail and learn different ways to apply Kubernetes manifests. Building upon the YAML files from the previous step, we will demonstrate various techniques for applying manifests.

Now, let's create a manifest for a simple web application that includes both a Deployment and a Service in a single file. Create a new file named  `web-app.yaml`  using  `nano`:

```bash
nano web-app.yaml
```

Add the following content to  `web-app.yaml`:

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-app
spec:
  replicas: 2
  selector:
    matchLabels:
      app: web
  template:
    metadata:
      labels:
        app: web
    spec:
      containers:
        - name: web
          image: nginx:alpine
          ports:
            - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: web-service
spec:
  selector:
    app: web
  type: ClusterIP
  ports:
    - port: 80
      targetPort: 80
```

This manifest defines two Kubernetes resources in a single file, separated by  `---`. This is a common way to group related resources together. Let's break down what's new:

-   **Multiple Resources in One File**: The  `web-app.yaml`  file now contains two separate Kubernetes resource definitions: a Deployment and a Service. The  `---`  separator is used to distinguish between them.
-   **`kind: Service`**: This defines a Service resource.
    -   **`spec.selector.app: web`**: This Service will target pods that have the label  `app: web`. This matches the labels we set for the pods created by the  `web-app`  Deployment.
    -   **`spec.type: ClusterIP`**: Specifies the service type as  `ClusterIP`. This means the service will be exposed on an internal IP address within the cluster and is typically used for communication between services within the cluster.
    -   **`spec.ports`**: Defines how the service maps ports to the target pods.
        -   **`port: 80`**: The port on the Service itself that you will access.
        -   **`targetPort: 80`**: The port on the target pods that the service will forward traffic to.

Now, let's apply this manifest using different methods.

**Method 1: Apply the entire file**

This is the most common way to apply a manifest. Use  `kubectl apply -f`  followed by the filename:

```bash
kubectl apply -f web-app.yaml
```

**Method 2: Apply from a directory**

You can apply all manifests in a directory at once. If you have multiple manifest files in the  `manifests`  directory, you can apply them all by specifying the directory instead of a specific file:

```bash
kubectl apply -f .
```

The  `.`  represents the current directory.  `kubectl`  will look for YAML files in this directory and apply all of them. This is useful when you have organized your manifests into multiple files within a directory.

**Method 3: Apply from a URL (Optional)**

`kubectl apply`  can also apply manifests directly from a URL. This is useful for quickly deploying example applications or configurations hosted online. For example, you can deploy the Redis master deployment from the Kubernetes examples repository:

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/examples/refs/heads/master/web/guestbook/redis-master-service.yaml
```

This will download the manifest from the URL and apply it to your cluster. Note: Be cautious when applying manifests from untrusted URLs as they can potentially modify your cluster.

Let's explore some additional options for  `kubectl apply`.

**Dry Run**

You can use the  `--dry-run=client`  flag to simulate applying a manifest without actually making changes to the cluster. This is useful for checking if your manifest is valid and for seeing what resources would be created or modified:

```bash
kubectl apply -f web-app.yaml --dry-run=client
```

This command will output what  _would_  be created or changed, but it won't actually apply the changes to your cluster.

**Verbose Output**

For more detailed output from  `kubectl apply`, you can use the  `-v`  flag followed by a verbosity level (e.g.,  `-v=7`). Higher verbosity levels provide more detailed information, which can be helpful for debugging:

```bash
kubectl apply -f web-app.yaml -v=7
```

This will print a lot more information about the API requests being made and the processing of the manifest.

Let's briefly discuss the difference between declarative and imperative management in Kubernetes, especially in the context of  `kubectl apply`  and  `kubectl create`.

-   **`kubectl apply`**: Uses a declarative approach. You define the desired state in your manifest files, and  `kubectl apply`  attempts to achieve that state. If you run  `kubectl apply`  multiple times with the same manifest, Kubernetes will only make changes if there are differences between the desired state in the manifest and the current state in the cluster.  `kubectl apply`  is generally recommended for managing Kubernetes resources because it is more robust and easier to manage changes over time. It tracks the configuration of your resources and allows for incremental updates.
-   **`kubectl create`**: Uses an imperative approach. You directly instruct Kubernetes to create a resource. If you try to run  `kubectl create`  for a resource that already exists, it will typically result in an error.  `kubectl create`  is less flexible for managing updates and changes compared to  `kubectl apply`.

In most cases, especially for managing application deployments,  **`kubectl apply`  is the preferred and recommended method**  due to its declarative nature and better handling of updates and configuration management.

### Verify the Deployment Status

In this step, you will learn how to inspect and verify the status of Kubernetes deployments and other resources using various  `kubectl`  commands. You will explore different ways to gather information about your running applications and their health within the Kubernetes cluster.

Let's start by listing all deployments in the current namespace (which is  `default`  unless you have changed it). Use  `kubectl get deployments`:

```bash
kubectl get deployments
```

This command provides a concise overview of your deployments. Example output:

```
NAME               READY   UP-TO-DATE   AVAILABLE   AGE
nginx-deployment   3/3     3            3           4m44s
redis-master       1/1     1            1           94s
web-app            2/2     2            2           113s
```

Here's what each column means:

-   `NAME`: The name of the deployment.
-   `READY`: Shows the number of ready replicas versus the desired number of replicas (e.g.,  `3/3`  means 3 desired replicas are ready).
-   `UP-TO-DATE`: Indicates how many replicas have been updated to the latest desired state.
-   `AVAILABLE`: Shows how many replicas are currently available to serve traffic.
-   `AGE`: How long the deployment has been running.

To get more detailed information in a wider format, you can use the  `-o wide`  flag with  `kubectl get deployments`:

```bash
kubectl get deployments -o wide
```

Example output:

```
NAME               READY   UP-TO-DATE   AVAILABLE   AGE     CONTAINERS   IMAGES                      SELECTOR
nginx-deployment   3/3     3            3           4m58s   nginx        nginx:latest                app=nginx
redis-master       1/1     1            1           108s    master       registry.k8s.io/redis:e2e   app=redis,role=master,tier=backend
web-app            2/2     2            2           2m7s    web          nginx:alpine                app=web
```

The  `-o wide`  output includes additional columns like  `CONTAINERS`,  `IMAGES`, and  `SELECTOR`, providing more context about the deployment.

To inspect the pods that are part of a specific deployment, you can use labels. Remember that in our  `web-app`  Deployment manifest, we set the label  `app: web`  for the pods. You can use this label to filter pods using  `kubectl get pods -l <label_selector>`. To see the pods associated with the  `web-app`  deployment, run:

```bash
kubectl get pods -l app=web
```

Example output:

```
NAME                      READY   STATUS    RESTARTS   AGE
web-app-xxx-yyy           1/1     Running   0          10m
web-app-xxx-zzz           1/1     Running   0          10m
```

This lists the pods that match the label selector  `app=web`, which are the pods managed by the  `web-app`  Deployment.

For in-depth information about a specific deployment, use  `kubectl describe deployment <deployment_name>`. Let's describe the  `web-app`  deployment:

```bash
kubectl describe deployment web-app
```

`kubectl describe`  provides a wealth of information about the deployment, including:

-   **Name, Namespace, CreationTimestamp, Labels, Annotations**: Basic metadata about the deployment.
-   **Selector**: The label selector used to identify pods managed by this deployment.
-   **Replicas**: Desired, updated, total, available, and unavailable replica counts.
-   **StrategyType, RollingUpdateStrategy**: Details about the update strategy (e.g., RollingUpdate).
-   **Pod Template**: The specification used to create pods for this deployment.
-   **Conditions**: Conditions indicating the status of the deployment (e.g.,  `Available`,  `Progressing`).
-   **Events**: A list of events related to the deployment, which can be helpful for troubleshooting.

Look at the  `Conditions`  and  `Events`  sections in the  `describe`  output. These sections often provide clues if there are issues with your deployment. For example, if a deployment is not becoming  `Available`, the  `Events`  might show errors related to image pulling, pod creation failures, etc.

To check the status of the Service, you can use similar commands. First, list all services:

```bash
kubectl get services
```

Example output:

```
NAME          TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
kubernetes    ClusterIP   10.96.0.1       <none>        443/TCP   10m
web-service   ClusterIP   10.106.220.33   <none>        80/TCP    2m47s
```

This shows the  `web-service`  along with its  `TYPE`  (`ClusterIP`),  `CLUSTER-IP`, and exposed  `PORT(S)`.

For more details about a service, use  `kubectl describe service <service_name>`:

```bash
kubectl describe service web-service
```

The  `describe service`  output includes:

-   **Name, Namespace, Labels, Annotations**: Basic metadata.
-   **Selector**: The label selector used to identify the target pods.
-   **Type**: The service type (`ClusterIP`  in this case).
-   **IP, IPs**: The cluster IP address assigned to the service.
-   **Port, TargetPort**: Port mappings defined for the service.
-   **Endpoints**: Shows the IP addresses and ports of the pods that are currently backing this service.  **This is very important**. If you don't see any endpoints, it means the service is not correctly connected to any pods, likely due to a selector mismatch.
-   **Session Affinity, Events**: Other service configurations and events.

When verifying the deployment status, key things to look for are:

-   **Deployment  `READY`  status**: Ensure it shows the desired number of replicas (e.g.,  `2/2`  for  `web-app`).
-   **Pod  `STATUS`**: All pods should be in  `Running`  status.
-   **Service  `Endpoints`**: Check that the service has endpoints and that they correspond to the IP addresses of your running pods. If there are no endpoints, troubleshoot the service's selector and pod labels.
-   **Check for warnings or errors**: Examine the output of  `kubectl describe deployment <deployment_name>`  and  `kubectl describe service <service_name>`  for any unusual conditions or errors in the  `Events`  sections.

By using these  `kubectl`  commands, you can effectively monitor and verify the status of your deployments and services in Kubernetes, ensuring your applications are running as expected.

### Access the Application using kubectl proxy

In this step, you will learn how to access your Kubernetes applications using  `kubectl proxy`.  `kubectl proxy`  creates a secure proxy connection to the Kubernetes API server, allowing you to access cluster services and pods from your current environment. This is very useful for development and debugging, especially when you want to access services that are not exposed externally.

Start the  `kubectl proxy`  in the background. This command will run the proxy in a separate process, allowing you to continue using your terminal.

```bash
kubectl proxy --port=8080 &
```

The  `&`  at the end of the command runs it in the background. You should see output like:

```
Starting to serve on 127.0.0.1:8080
```

If your terminal seems to hang after running this command, you might need to press  `Ctrl+C`  once to return to your prompt. The proxy should still be running in the background.

Now, let's find the names of the pods that are part of your  `web-app`  deployment. You can use  `kubectl get pods -l app=web`  again:

```bash
# Get pod names for the 'web-app'
kubectl get pods -l app=web
```

Example output:

```
NAME                      READY   STATUS    RESTARTS   AGE
web-app-xxx-yyy           1/1     Running   0          20m
web-app-xxx-zzz           1/1     Running   0          20m
```

Take note of one of the pod names, for example,  `web-app-xxx-yyy`. You will use this pod name to construct the API path to access the NGINX web server running in that pod.

Kubernetes API resources are accessible through specific paths. To access a pod through the  `kubectl proxy`, you need to construct a URL like this:

```
http://localhost:8080/api/v1/namespaces/<namespace>/pods/<pod_name>/proxy/
```

Let's break down this URL:

-   `http://localhost:8080`: The address where  `kubectl proxy`  is running. By default, it listens on port 8080 in your current environment.
-   `/api/v1`: Specifies the Kubernetes API version (`v1`).
-   `/namespaces/<namespace>`: The namespace where your pod is running. In our case, it's  `default`.
-   `/pods/<pod_name>`: The name of the pod you want to access. Replace  `<pod_name>`  with the actual name of your pod (e.g.,  `web-app-xxx-yyy`).
-   `/proxy/`: Indicates that you want to proxy a connection to the pod.

To make it easier to use the pod name in the URL, let's store the first pod's name in a shell variable. Run this command, which uses  `kubectl get pods`  and  `jsonpath`  to extract the name of the first pod with label  `app=web`:

```bash
# Get the name of the first pod with label 'app=web'
POD_NAME=$(kubectl get pods -l app=web -o jsonpath='{.items[0].metadata.name}')
echo $POD_NAME # Optional: print the pod name to verify
```

Now, you can use the  `$POD_NAME`  variable in your  `curl`  command to access the NGINX default page served by your pod. Use  `curl`  to send an HTTP request to the proxy URL. Replace  `${POD_NAME}`  in the URL with the variable we just set:

```bash
curl http://localhost:8080/api/v1/namespaces/default/pods/${POD_NAME}/proxy/
```

If everything is working correctly, this command should return the HTML content of the default NGINX welcome page. Example output will be HTML content starting with:

```html
<!doctype html>
<html>
  <head>
    <title>Welcome to nginx!</title>
    ...
  </head>
  <body>
    <h1>Welcome to nginx!</h1>
    ...
  </body>
</html>
```

This output confirms that you have successfully accessed the NGINX web server running inside your pod through the  `kubectl proxy`.

Let's explore a few more things you can do with  `kubectl proxy`.

**List all pods in the default namespace via proxy**:

You can access the Kubernetes API directly through the proxy. For example, to list all pods in the  `default`  namespace, you can use this URL:

```bash
curl http://localhost:8080/api/v1/namespaces/default/pods/
```

This will return a JSON response containing information about all pods in the  `default`  namespace.

**Get detailed information about a specific pod via proxy**:

To get detailed information about a specific pod (similar to  `kubectl describe pod`), you can access the pod's API endpoint directly:

```bash
curl http://localhost:8080/api/v1/namespaces/default/pods/${POD_NAME}
```

This will return a JSON response with detailed information about the specified pod.

**Stopping  `kubectl proxy`**:

When you are finished using  `kubectl proxy`, you should stop it. Since we started it in the background, you need to find its process ID (PID) and terminate it. You can use the  `jobs`  command to list background processes:

```bash
jobs
```

This will show you the background processes running from your current terminal session. You should see  `kubectl proxy`  listed. To stop it, you can use the  `kill`  command followed by the process ID. For example, if  `jobs`  shows  `[1] Running kubectl proxy --port=8080 &`, then the process ID is  `1`. You would use:

```bash
kill %1
```

Replace  `%1`  with the job ID shown by the  `jobs`  command. Alternatively, you can find the process ID using  `ps aux | grep kubectl proxy`  and then use  `kill <PID>`.

Key points to remember about  `kubectl proxy`:

-   `kubectl proxy`  creates a secure, authenticated connection to the Kubernetes API server.
-   It allows you to access cluster resources (pods, services, etc.) from your current environment as if you were inside the cluster network.
-   It's very useful for debugging, development, and exploring the Kubernetes API.
-   For security reasons,  `kubectl proxy`  is only accessible on  `localhost`  (127.0.0.1). It's not meant to be used for exposing services publicly.
