# K8s Resources
k8s has many resources which we can create and use. In this article we will explain `namespace` & `pod` and we will create them using CLI. 

## **Namespace**
Kubernetes Namespace is a way to divide and organize resources within a Kubernetes cluster. Think of it as a "virtual cluster" inside the actual cluster. It allows you to create isolated environments for different teams, projects, or applications, so they can operate independently.    
It let you group resources (like Pods, Services, Deployments, etc.) into logical units. For example, you can have a `development` namespace for testing and a `production` namespace for live apps. Resources in different namespaces can have the same name. For example, you can have a `web-app` Pod in both the `dev` and `prod` namespaces without conflict.      

Kubernetes comes with a few built-in namespaces, like:
- `default` (for resources without a specified namespace),   
- `kube-system` (for Kubernetes system components),   
- `kube-public` (for publicly accessible resources).

#### Examples:

**Create a Namespace**   
```bash
kubectl create namespace <namespace-name>
```   
**Describe Namespace**    
```bash
kubectl describe namespace <namespace-name>
```
**Get resource in Namespace**    
```bash
kubectl get <resource-type> -n <namespace-name>
```   
**List all Namespace**    
```bash
kubectl get namespace
```   
 **Delete a Namespace**    
```bash
kubectl delete namespace my-namespace
```
- This deletes **all resources** in the namespace.   


## **Pod**
A Pod is the smallest and simplest unit in Kubernetes. It’s like a "wrapper" for one or more containers that are tightly coupled and need to work together. Pods are the basic building blocks of applications in Kubernetes. Pods are ephemeral (means short-lived). If a Pod dies, it won’t restart unless managed by a higher-level resource (like a Deployment).   
Containers in the same Pod share `Network` (same IP address), `Storage` (can share volumes) & `Memory/CPU` (share the same resource limits and requests). Pods are often labeled (e.g., app=frontend) so that other Kubernetes components (like Services) can find and interact with them.


#### Examples:

**Create a Pod**
```bash
kubectl run <pod-name> --image=<container-image> --port=<port>
```
Example:
```bash
kubectl run my-pod --image=nginx --port=80
```

**List Pods**   
- List all Pods in the default Namespace:
```bash
kubectl get pods
```
- List Pods in a specific Namespace:
```bash
kubectl get pods -n <namespace>
```
- List Pods with more details (e.g., IP, node, status):
```bash
kubectl get pods -o wide
```
- List Pods across all Namespaces:
```bash
kubectl get pods --all-namespaces
```

**Describe a Pod**    
Get detailed information about a Pod:
```bash
kubectl describe pod <pod-name>
```

**View Pod Logs**   
- View logs for a single-container Pod:
```bash
kubectl logs <pod-name>
```
- View logs for a specific container in a multi-container Pod:
```bash
kubectl logs <pod-name> -c <container-name>
```
- Stream logs in real-time (follow):
```bash
kubectl logs -f <pod-name>
```

**Execute Commands in a Pod**   
- Open an interactive shell in a Pod:
```bash
kubectl exec -it <pod-name> -- /bin/sh
```
or for bash:
```bash
kubectl exec -it <pod-name> -- /bin/bash
```
- Run a one-off command in a Pod:
```bash
kubectl exec <pod-name> -- <command>
```
Example:
```bash
kubectl exec my-pod -- ls /app
```

**Delete a Pod**     
- Delete a specific Pod:
```bash
kubectl delete pod <pod-name>
```
- Delete all Pods in a Namespace:
```bash
kubectl delete pods --all -n <namespace>
```
- Delete a Pod defined in a YAML file:
```bash
kubectl delete -f pod.yaml
```

**Port Forwarding**   
- Forward a local port to a Pod’s port:
```bash
kubectl port-forward <pod-name> <local-port>:<pod-port>
```
Example:
```bash
kubectl port-forward my-pod 8080:80
```
This allows you to access the Pod locally at `http://localhost:8080`.

**Debugging a Pod**   
- Check why a Pod is not running (e.g., image pull errors, crashes):
```bash
kubectl describe pod <pod-name>
```
- Check the status of a Pod:
```bash
kubectl get pod <pod-name> -o yaml
```

**Delete a Pod**   
```bash
kubectl delete pod <pod-name>
```

**Label a Pod**   
- Add or update a label for a Pod:
```bash
kubectl label pod <pod-name> <key>=<value>
```
Example:
```bash
kubectl label pod my-pod env=production
```

**Watch Pods**   
- Watch Pods in real-time (e.g., for status changes):
```bash
kubectl get pods -w
```

**Check Resource Usage**   
- View CPU/memory usage for a Pod (requires Metrics Server):
```bash
kubectl top pod <pod-name>
```

## What's Next?

In Next Part, we will discuss Namespace & Pod.

[Next Article: Namespace & Pod →](res-1.md)
