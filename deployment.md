# Kubernetes Deployment

In Kubernetes, a **pod** is the smallest and simplest unit that we can deploy and manage. On other hand a **Deployment** is used to manages **pods** by ensuring the desired number of replicas (copies) of the pod are running at all times. The Deployment handles the creation, updating, and scaling of pods, while ensuring that if any pod fails or is terminated, Kubernetes automatically replaces it to maintain the desired number of pods.

**Features:**
- `Scaling`: You can change the number of replicas by updating the replicas field in the Deployment YAML. Kubernetes will automatically scale up or down to match the desired number of replicas.
- `Rolling Updates`: When you update the Docker image or configuration in the YAML, Kubernetes will gradually replace old Pods with new ones, without downtime, to ensure zero disruption.
- `Self-healing`: If a Pod fails (due to crash, network issues, etc.), Kubernetes will automatically replace the failed Pod to maintain the desired number of replicas.
- `Rollback`: If something goes wrong with a new version of the application, Kubernetes can roll back to the previous stable version of the Deployment.

### Deployment & ReplicaSet:
A Deployment is a higher-level controller that manages ReplicaSets. When you create a Deployment, it automatically creates a ReplicaSet to manage the Pods. The Deployment keeps track of the ReplicaSet, and the ReplicaSet makes sure the right number of Pods are always running.

#### **ReplicaSet**
ReplicaSet ensures that a specified number of identical Pods are running at any given time. It creates and manages the Pods. It makes sure that the desired number of Pods (replicas) are running and that they have the correct specifications (container image, ports, environment variables, etc.). When the Deployment is created, the ReplicaSet generates the Pods based on the Pod template defined in the Deployment. It maintains the desired number of replicas of Pods and automatically replaces any Pods that fail or are deleted. If you scale or update the Deployment, Kubernetes will modify the ReplicaSet to ensure the Pods match the new configuration.

---

### Create a Deployment

### 1.  Imperative Way

In Kubernetes, you can also create a deployment in an imperative way using the `kubectl` command. Here's the command to create a deployment with a specific image and other parameters:   
```bash
kubectl create deployment <deployment-name> --image=<image-name>
```   
**Example:**   
```bash
kubectl create deployment my-app --image=nginx:latest
```    
This creates a deployment of single Pod named `my-app` using single container of the `nginx:latest` image.

#### Additional Options:
- **Expose the deployment as a service**:
  If you want to expose your deployment as a service.   
  ```bash
  kubectl create deployment my-app --image=nginx:latest --port=80      # Container Listing Port 80
  kubectl expose deployment my-app --port=80 --target-port=80 --name=my-app-service
  ```   
- **Set the number of replicas**:
  You can set the number of replicas (copies of Pods) for your deployment:   
  ```bash
  kubectl create deployment my-app --image=nginx:latest --replicas=3 
  ```   

### 2.  Declarative Way

For long-term management, scalability, and reliability, declarative configurations (via YAML/JSON files) are better because they enable Kubernetes to enforce the desired state. 

Following is an example of Deployment YAML file.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-deployment
  labels:
    app: my-dep        # Deployment lable
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-app    # matchLable should be same as metadata.labels
  template:
    metadata:
      labels:
        app: my-app  #  same as matchLable
    spec:
      containers:
      - name: my-container
        image: nginx:latest
        ports:
        - containerPort: 80
```

Now, let's break it down into parts.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-deployment
  labels:
    app: my-dep
```
**apiVersion: apps/v1**  
This field specifies the API version of the resource. In this case, `apps/v1` is used for Deployments.

**kind: Deployment**  
This defines the type of Kubernetes resource being created. In this case, it's a **Deployment**.

**metadata:**  
Contains metadata about the Deployment, like its name, namespace, and labels.  
- **name**: `my-deployment` is the name given to the Deployment.
- **labels**: Labels are used to categorize or organize resources, here the label is `app: my-dep`.

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

**selector**   
A selector with `matchLabels` is used to select resources (such as Pods) based on their label values. It allows you to define a set of label key-value pairs, and Kubernetes will match the resources that have those labels.

- **For existing deployments**: If you have already created a Deployment of Pods using the YAML, Kubernetes will use this selector to find and select Pods that match the specified labels. If the Deployment’s configuration is changed (e.g., a change in the Pod template or other settings), Kubernetes will update those selected Pods accordingly.

- **For new deployments**: If you are creating the Deployment for the first time, Kubernetes will search for any Pods that already exist with matching labels and associate them with the new Deployment. If no matching Pods exist, Kubernetes will create new Pods based on the Pod template defined in the Deployment.


**matchLabels:**
  The **`matchLabels`** selector is used to match Pods that belong to a specific Deployment.
  
  - **Labels**: In this case, the label `app: my-app` is used to identify and target Pods that should be part of this Deployment.
  
  - **Function**: The `matchLabels` selector is used to ensure that Kubernetes identifies and targets specific resources based on the labels attached to them. Labels are key-value pairs that are applied to Kubernetes objects (e.g., Pods, Services, Deployments), which helps in **organizing**, **categorizing**, and **selecting** those objects for management or scaling purposes.


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
Describes the template for the Pods that will be created, if not already present in namespace. This is where the configuration for the Pods (like containers and ports) is specified.

**metadata (inside template):**  
Labels for Pods created by this Deployment. These labels are used for identifying and grouping Pods.  
- **labels**: The Pods will also have the label `app: my-app` to match the selector in the Deployment.

**spec (inside template):**  
Specifies the containers and other settings for the Pods.
  - **containers**: The list of containers that will run inside each Pod.  
    - **name**: `my-container` is the name of the container.  
    - **image**: `nginx:latest` is the Docker image for the container.  
    - **ports**: The container will expose port 80 to make the application accessible.


#### Note:
  
Above yaml contains 3 labels `Deployment.metadata.labels`, `selector.matchLabels` & `template.metadata.labels`.   
- `Deployment.metadata.labels` can be unique or different and serve the purpose of categorizing the Deployment resource itself.   
- `selector.matchLabels` and `template.metadata.labels` must always match because they are directly tied to the Pods the Deployment manages. If these labels do not match, Kubernetes will not be able to select and manage the Pods properly. Kubernetes uses these labels to ensure that the desired number of Pods are running, updated, and correctly replaced when necessary.
---

### How to Create a Deployment

To create a Deployment using the YAML file above, follow these steps:

1. Save the YAML file (e.g., `deployment.yaml`).
2. Use `kubectl` to apply the Deployment configuration to your Kubernetes cluster:
   ```bash
   kubectl apply -f deployment.yaml
   ```   
This command will create the Deployment and the associated Pods.

3. To see all resource /objects created by `Deployment` use following command
  ```bash
  kubectl get all -n <namespace-name>
  kubectl get all   # for default namespace 
  ```
The command will display details about the following types of resources, if they exist in the current namespace:   
Pods, Deployments, ReplicaSets, Services & others.

**output:**

```bash

NAME                                READY   STATUS    RESTARTS   AGE
pod/my-deployment-xxxxxxxxxx-xxxxx   1/1     Running   0          1m
pod/my-deployment-xxxxxxxxxx-xxxxx   1/1     Running   0          1m
pod/my-deployment-xxxxxxxxxx-xxxxx   1/1     Running   0          1m

NAME                            DESIRED   CURRENT   READY   AGE
deployment.apps/my-deployment   3         3         3       1m

NAME                                      DESIRED   CURRENT   READY   AGE
replicaset.apps/my-deployment-xxxxxxxxxx  3         3         3       1m
```

In this example:
- There are 3 Pods.
- There's one Deployment (`my-deployment`), which ensures that 3 Pods are running.
- The ReplicaSet is managing the Pods for the Deployment.
---

#### How Resources Are Created with a Deployment:
A **Deployment** is created based on the YAML configuration. The Deployment contains a Pod template, which specifies the containers, images, ports, and other settings for the Pods. Kubernetes creates an associated **ReplicaSet**.  
The ReplicaSet uses the Pod template from the Deployment to create the desired number of Pods (defined by the replicas field in the Deployment). The ReplicaSet then creates and manages the Pods. The Pods are scheduled to run on available Nodes in the Kubernetes cluster.  
The ReplicaSet ensures that the correct number of Pods is running. If any Pods crash or are deleted, the ReplicaSet automatically creates new Pods to replace them.

---

## What's Next?

In Next Part, we will discuss Labels, Selectors & Annotation.

[Next Article: Labels, Selectors & Annotation →](lsa.md)
  

