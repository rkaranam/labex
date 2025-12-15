## Explore the  `kubectl expose`  Command

The  `kubectl expose`  command is used to create a new Kubernetes service to expose an existing resource, such as a pod, deployment, or replication controller. It simplifies networking configurations by automatically creating services based on the provided resource.

Run the following command to view the available options for  `kubectl expose`:

```bash
kubectl expose -h
```

You will see the following output:

```plaintext
Expose a resource as a new Kubernetes service.

Looks up a deployment, service, replica set, replication controller or pod by name and uses the selector for that
resource as the selector for a new service on the specified port. A deployment or replica set will be exposed as a
service only if its selector is convertible to a selector that service supports, i.e. when the selector contains only
the matchLabels component. Note that if no port is specified via --port and the exposed resource has multiple ports, all
will be re-used by the new service. Also if no labels are specified, the new service will re-use the labels from the
resource it exposes.

Possible resources include (case insensitive):
  pod (po), service (svc), replicationcontroller (rc), deployment (deploy), replicaset (rs)

Examples:
  # Create a service for a replicated nginx, which serves on port 80 and connects to the containers on port 8000
  kubectl expose rc nginx --port=80 --target-port=8000

  # Create a service for a replication controller identified by type and name specified in "nginx-controller.yaml",
  which serves on port 80 and connects to the containers on port 8000
  kubectl expose -f nginx-controller.yaml --port=80 --target-port=8000

  # Create a service for a pod valid-pod, which serves on port 444 with the name "frontend"
  kubectl expose pod valid-pod --port=444 --name=frontend

  # Create a second service based on the above service, exposing the container port 8443 as port 443 with the name
  "nginx-https"
  kubectl expose service nginx --port=443 --target-port=8443 --name=nginx-https

  # Create a service for a replicated streaming application on port 4100 balancing UDP traffic and named 'video-stream'.
  kubectl expose rc streamer --port=4100 --protocol=UDP --name=video-stream

  # Create a service for a replicated nginx using replica set, which serves on port 80 and connects to the containers on
  port 8000
  kubectl expose rs nginx --port=80 --target-port=8000

  # Create a service for an nginx deployment, which serves on port 80 and connects to the containers on port 8000
  kubectl expose deployment nginx --port=80 --target-port=8000
```

### Expose the Deployment

To make the deployment accessible from outside the Kubernetes cluster, you need to expose it as a service. A service in Kubernetes acts as a stable network endpoint, even if the underlying pods change.

1.  Run the following command to create a NodePort service named  `hello-service`:
    
    ```bash
    kubectl expose deployment hello-world --name=hello-service --port=80 --target-port=80 --type=NodePort
    ```
    
    -   The  `--port`  flag specifies the port that the service will expose to external clients.
    -   The  `--target-port`  flag defines the port on the container where the service will route the traffic.
    -   The  `--type=NodePort`  flag makes the service accessible on a specific port on each node in the cluster.
2.  Verify that the service was created successfully:
    
    ```bash
    kubectl get services hello-service
    ```
    
    This command displays details of the service, including its type and the NodePort assigned.
    

Services like `NodePort` allow external clients to interact with pods in the Kubernetes cluster by forwarding requests to the correct container ports.

### Retrieve Service Details

To access the exposed service, you need the NodePort assigned to the service and the internal IP address of a node in the cluster. These details enable external clients to connect to your application.

1.  Retrieve the NodePort assigned to the  `hello-service`  by running:
    
    ```bash
    kubectl get service hello-service -o jsonpath='{.spec.ports[0].nodePort}'
    ```
    
    This command extracts the NodePort value from the service definition.
    
2.  Obtain the internal IP address of any node in the cluster using the following command:
    
    ```bash
    kubectl get nodes -o wide
    ```
    
    Note the  `INTERNAL-IP`  field in the output. This IP address represents the private network address of the node.
    

With the NodePort and InternalIP, you now have the necessary details to access the service externally.

### Access the Service

With the NodePort and the node's IP address, you can test the service using a tool like  `curl`  or a web browser.

1.  Replace  `<NODE_IP>`  and  `<NODE_PORT>`  with the values retrieved in the previous step, then run:
    
    ```bash
    curl <NODE_IP>:<NODE_PORT>
    ```
    
2.  If everything is configured correctly, you should see the default Nginx welcome page in the terminal. This output confirms that the service is running and accessible externally.
    

```html
<!doctype html>
<html>
  <head>
    <title>Welcome to nginx!</title>
    <style>
      html {
        color-scheme: light dark;
      }
      body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
      }
    </style>
  </head>
  <body>
    <h1>Welcome to nginx!</h1>
    <p>
      If you see this page, the nginx web server is successfully installed and
      working. Further configuration is required.
    </p>

    <p>
      For online documentation and support please refer to
      <a href="http://nginx.org/">nginx.org</a>.<br />
      Commercial support is available at
      <a href="http://nginx.com/">nginx.com</a>.
    </p>

    <p><em>Thank you for using nginx.</em></p>
  </body>
</html>
```

Accessing the service demonstrates how Kubernetes handles traffic routing between clients and the appropriate container ports.

### Cleanup Resources

After completing the lab, it's important to clean up the resources you created to avoid unnecessary resource consumption in your Kubernetes cluster.

- Delete all the created resources using the below command
    
    ```bash
    kubectl delete deploy/hello-world svc/hello-service
    ```
Cleaning up resources ensures that your cluster remains in a clean state, ready for future experiments.