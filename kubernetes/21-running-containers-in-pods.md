### Create a Pod with a Single Container

The first step is to create a Pod with a single container. To do this, you will create a YAML file that defines the Pod and its container.

```yml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod-1
spec:
  containers:
    - name: my-container
      image: nginx
```

Save the above code in a file named  `/home/labex/project/pod-single-container.yaml`  and execute the following command:

```bash
kubectl apply -f /home/labex/project/pod-single-container.yaml
```

This command will create a Pod named  `my-pod-1`  with a single container named  `my-container`  that runs the Nginx image.

### Create a Pod with Multiple Containers

The second step is to create a Pod with multiple containers. To do this, you will modify the previous YAML file to add another container.

```yml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod-2
spec:
  containers:
    - name: my-container
      image: nginx
    - name: my-sidecar
      image: busybox
      command: ["sh", "-c", "echo Hello from the sidecar! && sleep 3600"]
```

Save the above code in a file named  `/home/labex/project/pod-multiple-containers.yaml`  and execute the following command:

```bash
kubectl apply -f /home/labex/project/pod-multiple-containers.yaml
```

This command will create a Pod named  `my-pod-2`  with two containers. The first container runs the Nginx image, and the second container runs the BusyBox image and prints a message to the console.

### Create a Pod with Environment Variables

The third step is to create a Pod with environment variables. To do this, you will modify the YAML file to add environment variables to the Nginx container.

```yml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod-3
spec:
  containers:
    - name: my-container
      image: nginx
      env:
        - name: MY_ENV_VAR
          value: "Hello World!"
```

Save the above code in a file named  `/home/labex/project/pod-env-vars.yaml`  and execute the following command:

```bash
kubectl apply -f /home/labex/project/pod-env-vars.yaml
```

This command will create a Pod named  `my-pod-3`  with a single container named  `my-container`  that runs the Nginx image and has an environment variable named  `MY_ENV_VAR`  with the value  `Hello World!`.

### Create a Pod with Configmaps

The fourth step is to create a Pod with ConfigMaps. A ConfigMap is a Kubernetes resource that allows you to store configuration data (like environment variables, configuration files) separately from your application code. This separation makes it easier to change configuration without rebuilding your containers.

Let's break this into simple steps:

1.  **First, create a ConfigMap**:
    
    ```bash
    kubectl create configmap my-config --from-literal=MY_ENV_VAR=labex
    ```
    
    This command creates a ConfigMap named  `my-config`  that stores a single key-value pair:
    
    -   Key: MY_ENV_VAR
    -   Value: labex
    
    You can verify the ConfigMap was created and see its contents using:
    
    ```bash
    kubectl get configmap my-config -o yaml
    ```
    
2.  **Next, create a Pod that uses this ConfigMap**:
    
    ```yml
    apiVersion: v1
    kind: Pod
    metadata:
      name: my-pod-4
    spec:
      containers:
        - name: my-container
          image: nginx
          envFrom:
            - configMapRef:
                name: my-config
    ```
    
    Save this YAML in  `/home/labex/project/pod-configmap.yaml`  and apply it:
    
    ```bash
    kubectl apply -f /home/labex/project/pod-configmap.yaml
    ```
    

This will create a Pod that has access to the configuration value we stored in the ConfigMap. The value will be available as an environment variable inside the container. You can verify this by running:

```bash
kubectl exec -it my-pod-4 -- env | grep MY_ENV_VAR
```

### Create a Pod with Persistent Volumes

The fifth step is to create a Pod with a Persistent Volume (PV) and a Persistent Volume Claim (PVC). PVs and PVCs are used to store and access data persistently across Pod restarts.

To do this, you will first create a PV.

```yml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: my-pv
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: "/mnt/data"
```

Save the above code in a file named  `/home/labex/project/pv.yaml`  and execute the following command:

```bash
kubectl apply -f /home/labex/project/pv.yaml
```

This command will create a PV named  `my-pv`  with a capacity of 1Gi and a host path of  `/mnt/data`.

Next, you will create a PVC that requests 1Gi of storage from the PV.

```yml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
```

Save the above code in a file named  `/home/labex/project/pvc.yaml`  and execute the following command:

```bash
kubectl apply -f /home/labex/project/pvc.yaml
```

This command will create a PVC named  `my-pvc`  that requests 1Gi of storage.

Finally, you will modify the YAML file to add a volume and a volume mount to the Nginx container.

```yml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod-5
spec:
  containers:
    - name: my-container
      image: nginx
      volumeMounts:
        - name: my-volume
          mountPath: /mnt/data
  volumes:
    - name: my-volume
      persistentVolumeClaim:
        claimName: my-pvc
```

Save the above code in a file named  `/home/labex/project/pod-pv.yaml`  and execute the following command:

```bash
kubectl apply -f /home/labex/project/pod-pv.yaml
```

This command will create a Pod named  `my-pod-5`  with a single container named  `my-container`  that runs the Nginx image and has a volume mount at  `/mnt/data`  that is backed by the PVC named  `my-pvc`.