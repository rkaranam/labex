## Verify Cluster Setup

In this step, you'll learn how to verify your Kubernetes cluster's configuration and health using essential `kubectl` commands. These commands will help you understand the cluster's current state and connectivity.

First, check the cluster information:
```bash
kubectl cluster-info
```

This command provides details about the Kubernetes control plane and core services like CoreDNS.

Next, get a detailed view of the cluster nodes:

```bash
kubectl get nodes -o wide
```

Let's examine the node details more comprehensively:
```bash
kubectl describe node minikube
```

## Inspect Basic Cluster Resources

In this step, you will inspect basic Kubernetes resources—such as Pods, Deployments, and Services—across all namespaces. By using the  `-A`  (or  `--all-namespaces`) flag, you will see how resources are organized throughout the entire cluster. This is an excellent opportunity to introduce and understand the concept of  **Namespaces**  in Kubernetes.

**Namespaces: Resource Isolation**

Namespaces are logical partitions within a Kubernetes cluster that help organize and manage resources. They provide a way to group related objects and apply policies, access controls, and resource quotas at a granular level. By separating resources into different namespaces, you can:

-   **Improve Organization**: Group related workloads (e.g., by project, team, or environment—such as dev, test, and production).
-   **Enhance Security and Access Control**: Restrict which users or service accounts can view or modify resources in a particular namespace.
-   **Simplify Resource Management**: Apply resource limits, network policies, and other cluster-wide configurations more effectively.

When you list resources with the  `-A`  (or  `--all-namespaces`) flag, you’ll notice that components belonging to the Kubernetes system reside in the  `kube-system`  namespace, which is dedicated to cluster-level infrastructure. User-created applications typically reside in the  `default`  namespace or other custom namespaces that you define.

**Namespaces and Resources**

<img width="789" height="793" alt="Namespaces and Resources" src="https://github.com/user-attachments/assets/e8fc8862-32f3-4488-8e3e-7ab869c4aeb5" />

In the diagram:

-   The  **Control Plane**  manages the entire cluster, communicating with nodes and controlling workloads.
-   **Namespaces**  (such as  `kube-system`,  `default`, and  `dev`) logically separate resources within the cluster.
    -   `kube-system`  holds system-level components like CoreDNS and kube-dns.
    -   `default`  is commonly used for general workloads, here represented by a  `my-app`  deployment.
    -   `dev`  might represent a development environment, isolated from production workloads.

By viewing resources across all namespaces, you gain a comprehensive understanding of how these logical partitions help maintain an organized and secure cluster.

**Examples:**

List all pods across all namespaces:
```bash
kubectl get pods -A
```
Example output:
```
NAMESPACE     NAME                               READY   STATUS    RESTARTS      AGE
kube-system   coredns-787d4945fb-j8rhx           1/1     Running   0             20m
kube-system   etcd-minikube                      1/1     Running   0             20m
kube-system   kube-apiserver-minikube            1/1     Running   0             20m
kube-system   kube-controller-manager-minikube   1/1     Running   0             20m
kube-system   kube-proxy-xb9rz                   1/1     Running   0             20m
kube-system   kube-scheduler-minikube            1/1     Running   0             20m
kube-system   storage-provisioner                1/1     Running   1 (20m ago)   20m
```

Here, you see all system-related pods running in the  `kube-system`  namespace. If you had other deployments or services in different namespaces, they would appear in this list as well, each clearly scoped by their namespace.

List all deployments across all namespaces:
```bash
kubectl get deployments -A
```
Example output:

```
NAMESPACE     NAME      READY   UP-TO-DATE   AVAILABLE   AGE
kube-system   coredns   1/1     1            1           20m
```
The  `coredns`  deployment resides in the  `kube-system`  namespace.

Get a comprehensive view of all resources across all namespaces:

```bash
kubectl get all -A
```
This command displays an overview of pods, services, and deployments across different namespaces, helping you understand how these resources are distributed throughout the cluster.

**Key Takeaways:**

-   **Namespaces**  provide logical isolation and organization within a Kubernetes cluster.
-   Different Kubernetes components and resources are organized into specific namespaces (e.g.,  `kube-system`  for core services,  `default`  for general workloads, and additional namespaces you create).
-   By using  `-A`  to view resources across all namespaces, you gain insight into how your cluster is structured and how namespaces serve as boundaries for resource organization and access control.

By understanding how namespaces function as logical environments, you can better navigate, isolate, and manage your workloads and related cluster resources, especially as you scale your deployments and introduce more complexity into your Kubernetes environment.
