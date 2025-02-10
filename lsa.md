# Labels, Selectors & Annotation

## Labels & Selectors

**Labels** are key-value pairs attached to Kubernetes objects like Pods, Services & Deployments to organize and identify them. Labels help organize and categorize resources, making it easy to select (filter) or manage them using `selectors`.    

**Selectors** are used to filter resources based on their labels, enabling components like Services and Deployments to target specific Pods. There are two main types of selectors in Kubernetes:   
- `Equality-based selectors`: Select objects that have a label with a particular key and value. Match using `=`, `==`, or `!=`.   
- `Set-based selectors`: Select objects based on whether the label's value is in a set of values. Match using `in`, `notin`, `exists`, etc.  

Basic format to select / filter resource.
```bash
kubectl get pods -l env=production
```

---

### **Examples**

**Lets create a Pod with Labels**:
```yaml
# pod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    app: web
    env: prod
spec:
  containers:
  - name: nginx
    image: nginx
```
Apply it:
```bash
kubectl apply -f pod.yaml
```


**We can also create a Pod with Labels through Imperative method**:

```bash
kubectl run redis --image=redis -l app=db,env=prod
```

---

### Selecting / Filtering Resources based on there labels (Key-Value Pair)

**Equality-based**:
```bash
kubectl get pods -l app=web          # Pods labeled `app=web`
kubectl get pods -l env!=prod        # Pods where `env` is not `prod`
```

**Set-based**:
```bash
kubectl get pods -l "env in (prod, dev)"  # Pods in prod or dev
kubectl get pods -l "app in (web, db), tier!=backend"    # Pods is web or db and not in belong to beckend tier
```

---

### Modifying Labels

**Add/Update a Label**:
```bash
kubectl label pod nginx environment=test       # Add label
kubectl label pod nginx env=staging --overwrite  # Update existing label
```

**Label Resources in Bulk**:   
Use a selector to label multiple resources at once:
```bash
# Label all Pods in the "prod" environment with "backup=true"
kubectl label pods -l env=prod backup=true
```


**Remove a Label** (use `-` suffix):
```bash
kubectl label pod nginx env-  # Remove the `env` label
```

---

### Verify (Veiwing) Labels:   

You can check labels of any resource:
```bash
kubectl get <resource> <name> --show-labels
```

Example:
```bash
kubectl get pod nginx --show-labels
```

---


## Attonation

**Annotations** are key-value pairs used to attach **non-identifying metadata** to Kubernetes objects . Unlike labels, they **do not** filter or select resources but provide additional context / information that can be usefull.   

Attonation is used to:     
- Track tool-specific data (e.g., Git commit hashes, CI/CD pipeline IDs).  
- Add contact/owner info for debugging.  
- Store configuration data for third-party tools (e.g., Ingress controllers).  


#### Add Annotations in YAML   
```yaml
# deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deploy
  annotations:
    git-commit: "a1b2c3d"     # Git commit hash
    contact: "team@example.com"
spec:
  replicas: 2
  template:
    metadata:
    spec: 
```

#### Add/Update Annotations via CLI   

- **Add an annotation to a Pod**:  
  ```bash
  kubectl annotate pod my-pod description="Test pod for logging"
  ```

- **Update an annotation** (use `--overwrite`):  
  ```bash
  kubectl annotate pod my-pod description="Updated description" --overwrite
  ```

#### Remove an Annotation 

Use the annotation key with a `-` suffix:  
```bash
kubectl annotate pod my-pod description-
```

#### View Annotations   

```bash
kubectl describe pod my-pod | grep Annotations
```

### Common Annotation Patterns   
- **Ingress Annotations** (e.g., configuring SSL redirects):  
  ```yaml
  apiVersion: networking.k8s.io/v1
  kind: Ingress
  metadata:
    name: my-ingress
    annotations:
      nginx.ingress.kubernetes.io/ssl-redirect: "true"
  ```

- **Rollout Tracking**:  
  ```yaml
  metadata:
    annotations:
      example.com/deploy-timestamp: "2024-02-15T12:34:56Z"
  ```

---
