# Access your K8s Cluster

There are several ways to access a Kubernetes (K8s) cluster, typically depending on your use case and environment. Following listed are few of them.

- **kubectl (Command-Line Tool):**   
kubectl command-line tool is used to interact with your Kubernetes cluster. It allows you to run commands, deploy applications, inspect resources, and view logs.
kubectl is often used with a kubeconfig file that contains access details for the cluster.


- **Kubernetes Dashboard:**   
A web-based UI that allows you to manage and monitor Kubernetes clusters, view resources, and perform tasks like scaling and updating deployments.


- **Cloud Console (e.g., AWS EKS, Google GKE, Azure AKS):**   
Managed Kubernetes services provide a web interface (e.g., AWS Management Console, Google Cloud Console, Azure Portal) where you can configure and access your Kubernetes clusters.

- **API Server (Direct Access):**   
Kubernetes exposes an API server, which you can directly interact with via HTTP/HTTPS. You can use tools like curl or other API clients to interact with the cluster's REST API.


## kubectl 
We will use `kubectl` to interact with our k8s cluster. It communicates with the Kubernetes `API server`. Here's how kubectl works as a way to access your cluster:
- Communication with the Kubernetes API Server:     
  Kubernetes clusters are managed through the API server, which exposes a RESTful API. kubectl sends HTTP requests to the API server to perform operations. API server validates and processes these requests, then updates the cluster's state accordingly.
- Authentication and Authorization:     
  To access the cluster, kubectl needs to authenticate itself to the API server. This is typically done using a kubeconfig file (usually located at ~/.kube/config), which contains Cluster information, User credentials & Context (A combination of cluster, user, and namespace). Once authenticated the API server checks if the user is authorized to perform the requested actions.

The kubectl command follows a general format:
```bash
kubectl [command] [resource] [options]
```

- **kubectl**: The Kubernetes command-line interface for interacting with the cluster.

- **command**: The operation you want to perform. Common commands includes followings:   
`get`: List resources.   
`describe`: Get detailed information about a resource.    
`create`: Create a resource.    
`delete`: Remove a resource.    
`apply`: Apply configuration changes to resources.   
`exec`: Execute a command inside a container.   
`logs`: View logs from a pod/container.   
`scale`: Adjust the number of replicas of a resource.   
`expose`: Create a service to expose a resource. 

- **resource**: The type of Kubernetes resource you want to manage. Common resources includes followings:   
  `pod`, `deployment`, `service`, `namespace`, `node`, `configmap`, `secret`, `ingress`.
  
- **options**: Optional parameters that help specify what you’re doing. They can modify the behavior of the command. Some common options include:   
`--namespace=<namespace>`: Specify the namespace for the resource.   
`--selector=<label>`: Filter resources by label.   
`--kubeconfig=<path>`: Specify the kubeconfig file location


### Example Commands:
List Pods:   
```bash
kubectl get pods
```

List node complete details:   
```bash
kubectl get nodes -o wide
```

Describe a Deployment:   
```bash
kubectl describe deployment <deployment-name>
```

Create a Resource from a YAML File:   
```bash
kubectl create -f <file.yaml>
```

Apply Changes from a YAML File:   
```bash
kubectl apply -f <file.yaml>
```

Delete a Pod:   
```bash
kubectl delete pod <pod-name>
```

Get Logs from a Pod:   
```bash
kubectl logs <pod-name>
```

Scale a Deployment:   
```bash
kubectl scale deployment <deployment-name> --replicas=<number>
```

Run a Command Inside a Pod:   
```bash
kubectl exec -it <pod-name> -- <command>
```

Get Resources in a Specific Namespace:   
```bash
kubectl get pods --namespace=<namespace-name>
```

Output Resource Details in YAML Format:   
```bash
kubectl get pod <pod-name> -o yaml
```

### Other major commands   
These 3 commands are very usefull in resource creation and management.    

1. **`kubectl api-resources`**:
   
   Lists all available Kubernetes resource types (e.g., pods, services, deployments).
   
     ```bash
     kubectl api-resources
     ```

2. **`kubectl api-versions`**:
   
   Displays the API versions available in the cluster.
   
     ```bash
     kubectl api-versions
     ```

3. **`kubectl explain`**:
   
   Provides detailed explanations of Kubernetes resources and their fields.
   
     ```bash
     kubectl explain pod
     ```
     ```bash
     kubectl explain pod.spec.containers
     ```



   
