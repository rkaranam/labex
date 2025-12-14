
### Explore the  `kubectl set`  Command

The  `kubectl set`  command provides multiple subcommands to configure and modify application resources. It helps manage specific aspects like environment variables, container images, and resource settings.

1.  Run the following command to view the available  `kubectl set`  subcommands:
    
    ```bash
    kubectl set -h
    ```
    
    You will see the following output:
    
    ```plaintext
    Configure application resources.
    
    These commands help you make changes to existing application resources.
    
    Available Commands:
      env              Update environment variables on a pod template
      image            Update the image of a pod template
      resources        Update resource requests/limits on objects with pod templates
      selector         Set the selector on a resource
      serviceaccount   Update the service account of a resource
      subject          Update the user, group, or service account in a role binding or cluster role binding
    
    Usage:
      kubectl set SUBCOMMAND [options]
    
    Use "kubectl --help" for more information about a given command. Use "kubectl options" for a list of global command-line options (applies to all commands).
    ```
    
    Review the available subcommands and their descriptions to understand how  `kubectl set`  can be used.
    
2.  Use  `kubectl set --help`  to explore additional details about each subcommand as needed.

### Update the Container Image

Next, update the container image in the  `hello-world`  deployment to a specific version.

1.  Use the  `kubectl set`  command to update the container image to  `nginx:1.19.10`:
    
    ```bash
    kubectl set image deployment/hello-world nginx=nginx:1.19.10
    ```
    
    This command updates the  `nginx`  container in the  `hello-world`  deployment.
    
2.  Verify the image update by querying the container image:
    
    ```bash
    kubectl get deployment hello-world -o jsonpath='{.spec.template.spec.containers[0].image}'
    ```
    
    Ensure the output shows  `nginx:1.19.10`.

### Configure Resource Requests and Limits

Resource management is essential for Kubernetes deployments. Set resource requests and limits for the  `hello-world`  deployment.

1.  Configure CPU and memory requests and limits:
    
    ```bash
    kubectl set resources deployment/hello-world --limits=cpu=1,memory=512Mi --requests=cpu=500m,memory=256Mi
    ```
    
    This command sets resource requests to  `500m`  CPU and  `256Mi`  memory and limits to  `1`  CPU and  `512Mi`  memory.
    
2.  Verify the resource settings by describing the deployment:
    
    ```bash
    kubectl describe deployment hello-world
    ```
    
    Check the  `Limits`  and  `Requests`  sections in the output to confirm the configuration.

### Modify Labels on the Deployment

Labels help categorize and organize Kubernetes resources. Use the  `kubectl label`  command to add or modify labels on the deployment.

1.  Add a label  `environment=development`  to the  `hello-world`  deployment:
    
    ```bash
    kubectl label deployment hello-world environment=development
    ```
    
    This command adds a new label to the deployment.
    
2.  Verify that the label has been applied:
    
    ```bash
    kubectl get deployment hello-world --show-labels
    ```
    
    Check the  `LABELS`  column for the  `environment=development`  label.

### Update Annotations on the Deployment

Annotations provide metadata to Kubernetes resources. Use the  `kubectl annotate`  command to add or update annotations on the deployment.

1.  Add an annotation  `owner=team-alpha`  to the  `hello-world`  deployment:
    
    ```bash
    kubectl annotate deployment hello-world owner=team-alpha
    ```
    
    This command adds an annotation to the deployment.
    
2.  Verify that the annotation has been applied:
    
    ```bash
    kubectl describe deployment hello-world
    ```
    
    Check the  `Annotations`  section for  `owner=team-alpha`.