### Deploy a Sample Application

In this step, you'll learn how to create and deploy a simple web application using Kubernetes Deployment. We'll use an NGINX image as our sample application to demonstrate the deployment process.

First, navigate to the project directory:

```bash
cd ~/project
mkdir -p k8s-manifests
cd k8s-manifests
```

Create a new deployment manifest for a web application:

```bash
nano nginx-deployment.yaml
```

Add the following content to the file:

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-app
  labels:
    app: web
spec:
  replicas: 3
  selector:
    matchLabels:
      app: web
  template:
    metadata:
      labels:
        app: web
    spec:
      containers:
        - name: nginx
          image: nginx:1.23.3-alpine
          ports:
            - containerPort: 80
```

Save the file and exit the nano editor.

Deploy the application using kubectl:

```bash
kubectl apply -f nginx-deployment.yaml
```

Example output:

```
deployment.apps/web-app created
```

Verify the deployment:

```bash
kubectl get deployments
```

Example output:

```
NAME      READY   UP-TO-DATE   AVAILABLE   AGE
web-app   3/3     3            3           30s
```

Check the created pods:

```bash
kubectl get pods -l app=web
```

Example output:

```
NAME                      READY   STATUS    RESTARTS   AGE
web-app-xxx-yyy           1/1     Running   0          45s
web-app-xxx-zzz           1/1     Running   0          45s
web-app-xxx-www           1/1     Running   0          45s
```

Key points about this deployment:

1.  We created a Deployment with 3 replicas of an NGINX web server
2.  Used a specific, stable version of NGINX (1.23.3-alpine)
3.  Exposed container port 80
4.  Used labels to identify and manage the pods

### Update the Application Image in the Deployment YAML

In this step, you'll learn how to update the container image in a Kubernetes Deployment, simulating a real-world application upgrade scenario.

First, ensure you're in the correct directory:

```bash
cd ~/project/k8s-manifests
```

Open the existing deployment manifest:

```bash
nano nginx-deployment.yaml
```

Update the image from  `nginx:1.23.3-alpine`  to a newer version:

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-app
  labels:
    app: web
spec:
  replicas: 3
  selector:
    matchLabels:
      app: web
  template:
    metadata:
      labels:
        app: web
    spec:
      containers:
        - name: nginx
          image: nginx:1.24.0-alpine
          ports:
            - containerPort: 80
```

Apply the updated deployment:

```bash
kubectl apply -f nginx-deployment.yaml
```

Example output:

```
deployment.apps/web-app configured
```

Watch the deployment update process:

```bash
kubectl rollout status deployment web-app
```

Example output:

```
Waiting for deployment "web-app" to roll out...
Waiting for deployment spec update to be applied...
Waiting for available replicas to reach desired number...
deployment "web-app" successfully rolled out
```

Verify the new image version:

```bash
kubectl get pods -l app=web -o jsonpath='{.items[*].spec.containers[0].image}'
```

Example output:

```
nginx:1.24.0-alpine nginx:1.24.0-alpine nginx:1.24.0-alpine
```

Key points about image updates:

1.  Use  `kubectl apply`  to update deployments
2.  Kubernetes performs a rolling update by default
3.  Pods are replaced gradually to maintain application availability
4.  The update process ensures zero-downtime deployment

### Verify the Successful Update

In this step, you'll learn how to verify the successful update of your Kubernetes deployment by examining pod versions, status, and additional deployment details.

First, list the pods with detailed information:

```bash
kubectl get pods -l app=web -o wide
```

Example output:

```
NAME                      READY   STATUS    RESTARTS   AGE   IP           NODE       NOMINATED NODE   READINESS GATES
web-app-xxx-yyy           1/1     Running   0          3m    10.244.0.5   minikube   <none>           <none>
web-app-xxx-zzz           1/1     Running   0          3m    10.244.0.6   minikube   <none>           <none>
web-app-xxx-www           1/1     Running   0          3m    10.244.0.7   minikube   <none>           <none>
```

Check the specific image versions of the pods:

```bash
kubectl get pods -l app=web -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.spec.containers[0].image}{"\n"}{end}'
```

Example output:

```
web-app-xxx-yyy    nginx:1.24.0-alpine
web-app-xxx-zzz    nginx:1.24.0-alpine
web-app-xxx-www    nginx:1.24.0-alpine
```

Describe the deployment to get more detailed information:

```bash
kubectl describe deployment web-app
```

Example output:

```
Name:                   web-app
Namespace:              default
CreationTimestamp:      [current timestamp]
Labels:                 app=web
Annotations:            deployment.kubernetes.io/revision: 2
Replicas:               3 desired | 3 updated | 3 total | 3 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  app=web
  Containers:
   nginx:
    Image:        nginx:1.24.0-alpine
    Port:         80/TCP
    Host Port:    0/TCP
    Environment:  <none>
    Mounts:       <none>
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Available      True    MinimumReplicasAvailable
  Progressing    True    NewReplicaSetAvailable
```

Verify the rollout history:

```bash
kubectl rollout history deployment web-app
```

Example output:

```
REVISION  CHANGE-CAUSE
1         <none>
2         <none>
```

Key points about verification:

1.  All pods are running the new image version
2.  The deployment has 3 available replicas
3.  The rollout strategy ensures zero-downtime updates
4.  The deployment revision has been incremented

### Simulate and Diagnose Update Failures

In this step, you'll learn how to diagnose potential deployment update failures by simulating an problematic image update and using Kubernetes diagnostic tools.

First, navigate to the project directory:

```bash
cd ~/project/k8s-manifests
```

Create a deployment manifest with an invalid image:

```bash
nano problematic-deployment.yaml
```

Add the following content:

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: troubleshoot-app
  labels:
    app: troubleshoot
spec:
  replicas: 3
  selector:
    matchLabels:
      app: troubleshoot
  template:
    metadata:
      labels:
        app: troubleshoot
    spec:
      containers:
        - name: nginx
          image: nginx:non-existent-tag
          ports:
            - containerPort: 80
```

Apply the problematic deployment:

```bash
kubectl apply -f problematic-deployment.yaml
```

Example output:

```
deployment.apps/troubleshoot-app created
```

Check the deployment status:

```bash
kubectl rollout status deployment troubleshoot-app
```

Example output:

```
Waiting for deployment "troubleshoot-app" to roll out...
```

Press  `Ctrl+C`  to exit the rollout status.

Check the pod events and status:

```bash
kubectl get pods -l app=troubleshoot
```

Example output:

```
NAME                              READY   STATUS             RESTARTS   AGE
troubleshoot-app-6b8986c555-gcjj9   0/1     ImagePullBackOff   0          2m56s
troubleshoot-app-6b8986c555-p29dp   0/1     ImagePullBackOff   0          2m56s
troubleshoot-app-6b8986c555-vpv5q   0/1     ImagePullBackOff   0          2m56s
```

Examine pod details and logs:

```bash
# Replace 'xxx-yyy' with your actual pod name
POD_NAME=$(kubectl get pods -l app=troubleshoot -o jsonpath='{.items[0].metadata.name}')
kubectl describe pod $POD_NAME
kubectl logs $POD_NAME
```

You will see the pod status and logs indicating the image pull failure.

```plaintext
Failed to pull image "nginx:non-existent-tag"
```

Troubleshoot the issue by correcting the image:

```bash
nano problematic-deployment.yaml
```

Update the image to a valid tag:

```yml
image: nginx:1.24.0-alpine
```

Reapply the corrected deployment:

```bash
kubectl apply -f problematic-deployment.yaml
```

Check the pod status again:

```bash
kubectl get pods -l app=troubleshoot
```

Example output:

```
NAME                                READY   STATUS    RESTARTS   AGE
troubleshoot-app-5dc9b58d57-bvqbr   1/1     Running   0          5s
troubleshoot-app-5dc9b58d57-tdksb   1/1     Running   0          8s
troubleshoot-app-5dc9b58d57-xdq5n   1/1     Running   0          6s
```

Key points about diagnosing failures:

1.  Use  `kubectl describe`  to view deployment and pod events
2.  Check pod status for  `ImagePullBackOff`  or other error states
3.  Examine pod logs for detailed error information
4.  Verify image availability and tag correctness

### Rollback to a Stable Version

In this step, you'll learn how to rollback a Kubernetes deployment to a previous stable version using  `kubectl rollout undo`  command.

First, navigate to the project directory:

```bash
cd ~/project/k8s-manifests
```

Check the rollout history of the web application:

```bash
kubectl rollout history deployment web-app
```

Example output:

```
REVISION  CHANGE-CAUSE
1         <none>
2         <none>
```

Verify the current deployment details:

```bash
kubectl describe deployment web-app | grep Image
```

Example output:

```
    Image:        nginx:1.24.0-alpine

```

Perform the rollback to the previous revision:

```bash
kubectl rollout undo deployment web-app
```

Example output:

```
deployment.apps/web-app rolled back
```

Verify the rollback:

```bash
kubectl rollout status deployment web-app
```

Example output:

```
Waiting for deployment "web-app" to roll out...
deployment "web-app" successfully rolled out
```

Check the updated image version:

```bash
kubectl get pods -l app=web -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.spec.containers[0].image}{"\n"}{end}'
```

Example output:

```
web-app-xxx-yyy    nginx:1.23.3-alpine
web-app-xxx-zzz    nginx:1.23.3-alpine
web-app-xxx-www    nginx:1.23.3-alpine
```

Confirm the rollout history:

```bash
kubectl rollout history deployment web-app
```

Example output:

```
REVISION  CHANGE-CAUSE
2         <none>
3         <none>
```

Key points about rollback:

1.  `kubectl rollout undo`  reverts to the previous deployment revision
2.  Kubernetes maintains a history of deployment changes
3.  Rollback is performed with zero downtime
4.  The rollback creates a new revision in the history

### Adjust Rolling Update Strategy in the Deployment YAML

In this step, you'll learn how to customize the rolling update strategy in a Kubernetes Deployment to control how applications are updated and scaled.

First, navigate to the project directory:

```bash
cd ~/project/k8s-manifests
```

Create a new deployment manifest with custom rolling update strategy:

```bash
nano custom-rollout-deployment.yaml
```

Add the following content:

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-app-custom-rollout
  labels:
    app: web
spec:
  replicas: 5
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxUnavailable: 2
      maxSurge: 3
  selector:
    matchLabels:
      app: web
  template:
    metadata:
      labels:
        app: web
    spec:
      containers:
        - name: nginx
          image: nginx:1.24.0-alpine
          ports:
            - containerPort: 80
```

Apply the deployment:

```bash
kubectl apply -f custom-rollout-deployment.yaml
```

Example output:

```
deployment.apps/web-app-custom-rollout created
```

Verify the deployment status:

```bash
kubectl rollout status deployment web-app-custom-rollout
```

Example output:

```
Waiting for deployment "web-app-custom-rollout" to roll out...
deployment "web-app-custom-rollout" successfully rolled out
```

Describe the deployment to confirm the strategy:

```bash
kubectl describe deployment web-app-custom-rollout
```

Example output will include:

```
StrategyType:           RollingUpdate
RollingUpdateStrategy:  2 max unavailable, 3 max surge
```

Update the image to trigger a rolling update:

```bash
kubectl set image deployment/web-app-custom-rollout nginx=nginx:1.25.0-alpine
```

Monitor the update process:

```bash
kubectl rollout status deployment web-app-custom-rollout
```

Key points about rolling update strategy:

1.  `maxUnavailable`: Maximum number of pods that can be unavailable during update
2.  `maxSurge`: Maximum number of pods that can be created above the desired number
3.  Helps control update speed and application availability
4.  Allows fine-tuning of deployment behavior