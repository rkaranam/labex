# Kubernetes Architecture Overview

Kubernetes operates on a client-server model, with a centralized Control Plane governing the cluster’s state and a set of worker Nodes running workloads. At a high level, a user (often a developer) interacts with the Kubernetes cluster through command-line tools or APIs. The Control Plane makes decisions about what should run where, monitors the cluster’s health, and ensures that the desired state is achieved. The worker Nodes host your applications in Pods—groups of one or more containers—and offer the computational and storage resources needed to run them.

## Control Plane

This is the “brain” of the cluster, consisting of several components that work together to manage the entire system:

-   **kube-apiserver (API)**: Serves as the cluster’s front door. All administrative commands and resource requests pass through it.
-   **etcd (Key Value Store)**: Stores all configuration data and the current state of the cluster. If you lose etcd data, you lose the state of the cluster.
-   **kube-scheduler (SCH)**: Assigns Pods to Nodes based on resource requirements, constraints, and policies.
-   **kube-controller-manager (CTLM)**: Runs a variety of controllers that continually adjust the cluster’s state, ensuring that the actual state matches the desired state defined by your deployments and configurations.

## Nodes (Worker Machines)

Nodes are where workloads run. Each Node has:

-   **kubelet (KLT)**: A node-level agent that communicates with the Control Plane. It ensures Pods are running and reports their status back to the Control Plane.
-   **Container Runtime (CR)**: Software that runs and manages containers (e.g., Docker or containerd). It creates and manages containerized applications within Pods.

## Pods

A Pod is the smallest deployable unit in Kubernetes, typically representing a single instance of a running application. Pods can contain one or more containers that share the same network namespace and storage volumes.

## Services

A Service is an abstraction that defines a logical set of Pods and a policy for how to access them. Services provide stable IP addresses, DNS names, and load-balancing, ensuring that external consumers and other cluster components can reliably connect to your applications—even as Pods move between Nodes or are replaced during scaling or rolling updates.

## Interacting with the Cluster

-   Developers and administrators interact with the cluster through the  **kube-apiserver**, often using  `kubectl`  or other Kubernetes clients.
-   When a new application is deployed, the Control Plane components (scheduler, controllers) work to place the Pods on the appropriate Nodes.
-   The kubelet on each Node ensures Pods are healthy and running as instructed.
-   Services route traffic to the correct Pods, allowing clients to access applications without having to track Pod location changes.

<img width="1167" height="607" alt="Kubernetes Architecture" src="https://github.com/user-attachments/assets/c23f73ec-77b4-475b-a68e-6320f4e71aef" />

In the diagram:

-   The developer interacts with the  `kube-apiserver`  (API) through a CLI tool like  `kubectl`.
-   The Control Plane components (API, etcd, Scheduler, Controller Manager) manage the cluster state and orchestrate workloads.
-   Each Worker Node runs a kubelet and a container runtime, hosting multiple Pods.
-   A Service routes external or internal traffic to the correct Pods, providing a stable endpoint that abstracts away the complexity of Pod lifecycles and IP changes.



