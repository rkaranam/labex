### Create a Deployment

1.  Create a file called  `my-deployment.yaml`  in  `/home/labex/project/`  with the following content::

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
        - name: my-app
          image: nginx:latest
          ports:
            - containerPort: 80
```

This YAML file defines a Deployment with 3 replicas, running an Nginx container. The  `selector`  field selects the Pods controlled by the Deployment based on the  `app`  label.

2.  Deploy the  `my-deployment`  Deployment:

```bash
kubectl apply -f my-deployment.yaml
```

This will create the  `my-deployment`  Deployment and its associated ReplicaSets and Pods.

3.  Verify that the Deployment has been created:

```bash
kubectl get deployments
```

This will show you the Deployments in your cluster, including the  `my-deployment`  Deployment.

### Scale the Deployment

1.  Scale up the  `my-deployment`  Deployment to 5 replicas:

```bash
kubectl scale deployment my-deployment --replicas=5
```

This will increase the number of replicas in the  `my-deployment`  Deployment to 5.

2.  Verify that the Deployment has been scaled:

```bash
kubectl get deployments
```

This will show you the Deployments in your cluster, including the  `my-deployment`  Deployment with 5 replicas.

### Update the Deployment

1.  Edit the  `my-deployment`  Deployment to use the  `nginx:1.19`  image:

```bash
kubectl edit deployment my-deployment
```

This will open the Deployment in your default text editor. Change the  `image`  field to  `nginx:1.19`  and save the file.

2.  Verify that the Deployment has been updated:

```bash
kubectl rollout status deployment/my-deployment
```

This will show you the status of the latest rollout of the  `my-deployment`  Deployment.

### Roll Back the Deployment

1.  Roll back the  `my-deployment`  Deployment to the previous version:

```bash
kubectl rollout undo deployment/my-deployment
```

This will roll back the  `my-deployment`  Deployment to the previous version.

2.  Verify that the Deployment has been rolled back:

```bash
kubectl rollout status deployment/my-deployment
```

This will show you the status of the latest rollout of the  `my-deployment`  Deployment.

