# Kubernetes Deployment

A Kubernetes Deployments are a fundamental building block for managing containerized applications in a Kubernetes cluster. 
A Deployment is an API resource and a higher-level abstraction that provides a `declarative way to manage and scale a set of identical pods`. 
It provides declarative updates to Pods and ReplicaSets, ensuring that the desired number of replicas are running and that Pods are replaced automatically if they fail or need to be updated.    
Deployments are commonly used for managing stateless applications, such as web servers or microservices, because they handle rolling updates, scaling, and self-healing (restarting Pods when they fail).    

**Features:**   
- `Scaling`: You can change the number of replicas by updating the replicas field in the Deployment YAML. Kubernetes will automatically scale up or down to match the desired number of replicas.
- `Rolling Updates`: When you update the Docker image or configuration in the YAML, Kubernetes will gradually replace old Pods with new ones, without downtime, to ensure zero disruption.
- `Self-healing`: If a Pod fails (due to crash, network issues, etc.), Kubernetes will automatically replace the failed Pod to maintain the desired number of replicas.
- `Rollback`: If something goes wrong with a new version of the application, Kubernetes can roll back to the previous stable version of the Deployment.

--- 

## Other Resouces created with Deployment   
As we already disscussed that deployment provide a way to manage & scale set of identical pods so when we create a Kubernetes Deployment, Kubernetes not only creates the Deployment resource itself but also a few other resources under the hood to ensure the application is running in the desired state which are:

- `ReplicaSet`
- `Pods`

#### Pod   
As we already discussed in previous articles waht is Pod now lets discuss how Pods are Managed by Deployemnt through ReplicaSets.

#### **ReplicaSet**   
The ReplicaSet is closely tied to the Deployment resource, it ensures that a specified number of identical Pods are running at any given time. It creates and manages the Pods. It makes sure that the desired number of Pods (replicas) are running and that they have the correct specifications (container image, ports, environment variables, etc.). When the Deployment is created, the ReplicaSet generates the Pods based on the Pod template defined in the Deployment. It maintains the desired number of replicas of Pods and automatically replaces any Pods that fail or are deleted. If you scale or update the Deployment, Kubernetes will modify the ReplicaSet to ensure the Pods match the new configuration.


---

## Create a Deployment in YAML

A Deployment YAML defines how Kubernetes should manage Pods over time.

```yaml
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
      - name: my-container
        image: nginx:latest
        ports:
        - containerPort: 80
```



Now lets break it down in parts.   

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-deployment
  labels:
    app: my-app
```
**apiVersion: apps/v1**      
This field specifies the API version of the resource. In this case, `apps/v1` is used for Deployments.

**kind: Deployment**   
This defines the type of Kubernetes resource being created. In this case, it's a **Deployment**.

**metadata:**   
Contains metadata about the Deployment, like its name, namespace, and labels. 
- **name**: `my-app-deployment` is the name given to the Deployment.
- **labels**: Labels are used to categorize or organize resources, here the label is `app: my-app`.


```yaml
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-app
```

**spec:**   
Describes the desired state of the Deployment, which includes details like the number of replicas, selectors, and the Pod template. The spec is where you define the Deployment's characteristics, such as how many replicas to run and how the Pods should look.

**replicas: 3**   
This field specifies the number of Pods that should be running. Here, Kubernetes will maintain 3 replicas of the Pod.

**selector:**   
a selector with `matchLabels` is used to select resources (such as pods) based on their label values. It allows you to define a set of label key-value pairs, and Kubernetes will match the resources that have those labels. 
Lets say you already created deployment of pods using above YAML than k8s will use this selector with to search and select Pods and modify them is you change in YAML. If cearting 1st time then it search for already present Pods with similar tags to add them to Deployment. 
  - **matchLabels**: The `app: my-app` label is used to match Pods that belong to this Deployment.
    The `matchLabels` selector is used in Kubernetes to identify and target specific resources based on their labels. Labels are key-value pairs that are attached to Kubernetes objects (such as Pods, Services, Deployments, etc.) and are used for organizing, categorizing, and selecting those objects.


```yaml
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: my-container
        image: nginx:latest
        ports:
        - containerPort: 80
```

**template:**     
Describes the template for the Pods that will be created. This is where the configuration for the Pods (like containers and ports) is specified.

**metadata (inside template):**   
Labels for Pods created by this Deployment. These labels are used for identifying and grouping Pods.    
- **labels**: The Pods will also have the label `app: my-app` to match the selector in the Deployment.

**spec (inside template):**   
Specifies the containers and other settings for the Pods.
  - **containers**: The list of containers that will run inside each Pod.   
    - **name**: `my-container` is the name of the container.   
    - **image**: `nginx:latest` is the Docker image for the container.   
    - **ports**: The container will expose port 80 to make the application accessible.   


YAML with each field function.

```yaml
apiVersion: apps/v1          # Specifies the API version (apps/v1 for Deployments)
kind: Deployment             # Specifies the type of resource: a Deployment
metadata:
  name: my-app-deployment    # Name of the Deployment
  labels:                    # Labels for organizing resources
    app: my-app
spec:
  replicas: 3                # Specifies the number of Pods (replicas) to maintain
  selector:                  # Defines the label selector for Pods managed by the Deployment
    matchLabels:
      app: my-app
  template:                  # Defines the Pod template to create Pods with
    metadata:
      labels:                # Labels to apply to the Pods
        app: my-app
    spec:
      containers:            # List of containers inside the Pod
      - name: my-app-container  # Name of the container
        image: my-app-image:latest  # Docker image for the container
        ports:
        - containerPort: 80      # Exposes container's port to the Pod's IP
```

### How to Create a Deployment

To create a Deployment using the YAML file above, follow these steps:

1. Save the YAML file (e.g., `deployment.yaml`).
2. Use `kubectl` to apply the Deployment configuration to your Kubernetes cluster:
   ```bash
   kubectl apply -f deployment.yaml
   ```

This command will create the Deployment and the associated Pods.

---
#### How Resources are Created with a Deployment:
Deployment is created based on the YAML configuration. The Deployment contains a Pod template, which specifies the containers, images, ports, and other settings for the Pods. Kubernetes creates an associated ReplicaSet. 
The ReplicaSet uses the Pod template from the Deployment to create the desired number of Pods (defined by the replicas field in the Deployment). The ReplicaSet then creates and manages the Pods. The Pods are scheduled to run on available Nodes in the Kubernetes cluster.
The ReplicaSet ensures that the correct number of Pods is running. If any Pods crash or are deleted, the ReplicaSet automatically creates new Pods to replace them.



