To create a Pod in Kubernetes using imperative mode, the `kubectl run` command is utilized. This command directly instructs Kubernetes to create a Pod with the specified parameters.

Here is the basic syntax for creating a Pod:
```bash
kubectl run <pod-name> --image=<container-image> [options]
```
**Explanation of components:**
- `<pod-name>:` The desired name for your Pod.
- `<container-image>:` The Docker image to use for the container within the Pod **(e.g., nginx, busybox)**.
- `[options]:` Optional flags to further configure the Pod.

**Common options include:**
- `--port=<container-port>:` Exposes a specific port within the container.
- `--labels=<key1=value1,key2=value2>:` Assigns labels to the Pod for organization and selection.
- `--restart=Never:` Specifies that the Pod should not be restarted automatically if it terminates. This is often used for one-off tasks.
- `--env=<key=value>:` Sets environment variables within the container.
- `--command="<command>":` Overrides the default command of the container image.
- `--args="<arg1>,<arg2>":` Provides arguments to the container's command.

**Example:**

To create a Pod named `my-nginx-pod` running the `nginx` image, exposing port 80, and with a label `app=nginx`:

```bash
kubectl run my-nginx-pod --image=nginx --port=80 --labels="app=nginx"
```

To create a Pod named my-busybox-pod that runs a simple command and then exits:

```bash
kubectl run my-busybox-pod --image=busybox --restart=Never --command -- sleep 30
```

After running the command, you can verify the Pod's status using:

```bash
kubectl get pods
````
