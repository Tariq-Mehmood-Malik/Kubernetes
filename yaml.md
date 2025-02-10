# Kubernetes YAML

In Kubernetes, when we define resources using `YAML` or `JSON` files, we use the **AKMS** structure, which stands for `apiVersion`, `kind`, `metadata`, and `spec`(data field in some resouces). 
These four fields are essential for defining the desired state of your Kubernetes resources. Lets break down each field to understand it deeply.

```yaml
apiVersion:
kind:
metadata:
spec:
```

### 1. **apiVersion**
The **apiVersion** field specifies which version of the Kubernetes API to use for managing the resource. This ensures compatibility with Kubernetes' evolving API.    
You can use this command to find version of the Kubernetes API for your resource.
```bash
kubectl api-resources
```

**API version in YAML for Pod**
```yaml
apiVersion: v1    # API version for Pod
```

### 2. **kind**
The **kind** field defines the type of resource you're creating, such as **Pod**, **Deployment**, **Service**, etc.

**Example:**
```yaml
kind: Pod
```

### 3. **metadata**
The **metadata** field is used to provides important information for resource like the **name**, **namespace**, and **labels**, it helps to identify and manage resource.

**Example:**
```yaml
metadata:
  name: my-pod    # Custom name for your resouce
  labels:         # Labels are used to provide key & value pairs which are use to filter resources in cluster.
    app: nginx    # This resource has 1 label with key `app` & value `nginx`   
```

### 4. **spec**
The **spec** field describes the desired state of the resource, including configuration details like the number of **replicas**, **containers** and **ports**. 
This is where you define how you want the resource to behave.

**Example:**
```yaml
spec:
  containers:     # Define container details
  - name: nginx    # Conatiner name 
    image: nginx:latest     # Image to use 
    ports:               
    - containerPort: 80    # Container port in pod
```

### Complete Example:
Here’s how it all comes together in a Kubernetes **Pod** definition:

```yaml
apiVersion: v1              # Specifies the API version to use
kind: Pod                   # Defines the resource type
metadata:
  name: my-pod               # The name of the Pod. This uniquely identifies the Pod.
  labels:                    # Labels are key-value pairs used to organize and select resources.
    app: nginx-pod           # A label to associate the Pod with the 'nginx-pod' application.
spec:
    containers:              # Defines the containers to be run within the Pod.
    - name: my-app-container  # The name of the container.
      image: my-app-image:latest  # The Docker image for the container.
      ports:
      - containerPort: 80     # The container will expose port 80.
```

- **apiVersion**: Specifies the API version.
- **kind**: Defines the resource type.
- **metadata**: Contains metadata like name, labels.
- **spec**: Describes the desired state and configuration.

---

## What's Next?

In Next Part, we will discuss Kubernets Deployment.

[Next Article: Kubernets Deployment →](deployment.md)
