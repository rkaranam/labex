
### Taints and Tolerations

Taints and tolerations can be used to control which pods can be scheduled on which nodes in your cluster. A taint is a special label that marks a node as unsuitable for certain types of pods, and a toleration is a setting that allows a pod to be scheduled on a node with a matching taint.

1.  To view the taints for a specific node, run the following command:
    
    ```bash
    kubectl describe node minikube | grep Taints
    ```
    
    This will display the taints for the specified node.
    
2.  To add a taint to a node, run the following command:
    
    ```bash
    kubectl taint node minikube app=backend:NoSchedule
    ```
    
3.  Create a toleration to a pod, run the following command:
    
    ```bash
    cat << EOF | kubectl apply -f -
    apiVersion: v1
    kind: Pod
    metadata:
      name: my-pod
    spec:
      containers:
        - name: my-container
          image: nginx
      tolerations:
        - key: app
          operator: Exists
          effect: NoSchedule
    EOF
    ```
    
    This pod uses  `app`  as the name of the taint and  `NoSchedule`  as the effect the taint should have.

### View Node Capacity and Resource Usage

To view the available resources on a node, use the following command:

```bash
kubectl describe node minikube | grep -A 8 "Allocated resources"
```

Replace  `minikube`  with the name of the node you want to examine.

This will provide detailed information about the node, including its capacity and current resource usage.

### View Node Events

In Kubernetes, you can use the following command to filter all events related to a specific node:

```bash
kubectl get events --field-selector involvedObject.kind=Node,involvedObject.name=minikube
```

Replace  `minikube`  with the name of the node you want to query. This command will list all events related to that node, such as restarts, upgrades, and so on.


### Cordon and Uncordon a Node

In some cases, you may need to take a node out of rotation for maintenance or other reasons. Kubernetes provides a way to mark a node as unschedulable so that no new pods are scheduled on it. This is called "cordon".

To cordon a node, use the following command:

```bash
kubectl cordon minikube
```

Replace  `minikube`  with the name of the node you want to cordon.

Then Use the following command to check the node status:

```bash
kubectl get node
```

To uncordon a node and allow new pods to be scheduled on it, use the following command:

```bash
kubectl uncordon minikube
```

Replace  `minikube`  with the name of the node you want to uncordon.

Note that cordoning a node does not automatically move any existing pods off the node. You should manually delete or move the pods before cordoning the node to avoid any disruption.

