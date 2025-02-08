# K8s Resources
k8s has many resources which we can create and use. In this article we will explain `namespace`, `pod` & `deployment` and create them using CLI 

### **Namespace**
It is a virtual cluster within a physical cluster. Namespaces is used to isolate resources (separate environments for `dev` and `prod`). Resources (like Pods, Deployments) belong to a namespace. The `default` namespace is used if none is specified.


### **Pod**
The smallest deployable unit in Kubernetes. A Pod runs one or more containers (a single instance of your app). Pods are ephemeral (means short-lived). If a Pod dies, it won’t restart unless managed by a higher-level resource (like a Deployment).



### **Deployment**
It is also used to create and manages Pods. It is used b/c it ensures that a specified number of replicas of pods are running. It also handles updates, rollbacks, and scaling. It is preferred way to manage stateless apps in Kubernetes.

---

#### **Step 1: Create a Namespace**
```bash
kubectl create namespace my-namespace
```

#### **Step 2: Create a Pod in the Namespace**
```bash
kubectl run my-pod --image=nginx --restart=Never -n my-namespace
```
- `--image=nginx`: Uses the `nginx` container image.
- `--restart=Never`: Ensures this is a standalone Pod (not managed by a Deployment).
- `-n my-namespace`: Places the Pod in the `my-namespace` namespace.

#### **Step 3: Create a Deployment in the Namespace**
```bash
kubectl create deployment my-deployment --image=nginx --replicas=3 -n my-namespace
```
- `--replicas=3`: Ensures 3 Pod replicas are always running.
- The Deployment will auto-manage its Pods (no need to create Pods manually).

---

### **3. Monitor Resources via CLI**
#### **Check Pod Status**
```bash
kubectl get pods -n my-namespace
```
- Add `--watch` to monitor changes in real time:
  ```bash
  kubectl get pods -n my-namespace --watch
  ```

#### **Check Deployment Status**
```bash
kubectl get deployments -n my-namespace
```
- See how many Pods are ready:
  ```bash
  kubectl describe deployment my-deployment -n my-namespace
  ```

#### **View Pod Logs**
```bash
kubectl logs my-pod -n my-namespace
```

#### **Execute Commands in a Pod**
```bash
kubectl exec -it my-pod -n my-namespace -- /bin/bash
```
- `-it`: Interactive terminal.
- Replace `/bin/bash` with your container’s shell (e.g., `/bin/sh`).

---

### **4. Common Operations**
#### **Scale a Deployment**
```bash
kubectl scale deployment my-deployment --replicas=5 -n my-namespace
```

#### **Delete a Pod**
```bash
kubectl delete pod my-pod -n my-namespace
```
- The Deployment will auto-recreate its Pods if they crash or are deleted.

#### **Delete a Deployment**
```bash
kubectl delete deployment my-deployment -n my-namespace
```

#### **Delete a Namespace**
```bash
kubectl delete namespace my-namespace
```
- This deletes **all resources** in the namespace.

---

### **Example Workflow**
1. **Create Namespace**:
   ```bash
   kubectl create namespace demo
   ```

2. **Create Deployment**:
   ```bash
   kubectl create deployment demo-app --image=nginx --replicas=3 -n demo
   ```

3. **Monitor Deployment**:
   ```bash
   kubectl get pods -n demo --watch
   ```

4. **Scale Up**:
   ```bash
   kubectl scale deployment demo-app --replicas=5 -n demo
   ```

5. **Cleanup**:
   ```bash
   kubectl delete namespace demo
   ```
