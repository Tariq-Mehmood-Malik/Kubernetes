 # Kubernetes Resources & Objects

In Kubernetes (K8s), the terms resources and objects are often used interchangeably, but they actually have different meanings. Kubernetes uses a variety of resources and objects to manage applications, networking, and configurations within the cluster. 

- **Kubernetes Resources** are the types (or categories) of components that Kubernetes manages (like Pod, Service, Deployment).   
- **Kubernetes Objects** are the instances or real configurations of these resources (like a specific Pod or Deployment with set parameters).   

In future article we will discuss these resources in details but there basic definations are as follow.

### 1. **Pod**
   - **Resource**: Pod
   - **Object**: An instance of a Pod
   - **Definition**: The smallest deployable unit in Kubernetes, a Pod is a group of one or more containers that share storage, networking, and a specification for how to run the containers. Each Pod has a unique IP address and can host multiple containers that communicate with each other via localhost.
  
### 2. **ReplicaSet**
   - **Resource**: ReplicaSet
   - **Object**: A specific ReplicaSet instance
   - **Definition**: Ensures that a specified number of replicas of a Pod are running at any given time. It maintains the desired state of Pods, automatically creating or deleting Pods to match the desired replica count.

### 3. **Deployment**
   - **Resource**: Deployment
   - **Object**: A specific Deployment instance
   - **Definition**: Manages the deployment of Pods and ReplicaSets. It provides declarative updates to Pods and ReplicaSets, allowing you to roll out and roll back changes to applications smoothly. 

### 4. **StatefulSet**
   - **Resource**: StatefulSet
   - **Object**: A specific StatefulSet instance
   - **Definition**: Used to manage stateful applications, ensuring that the order of deployment, scaling, and updates is preserved. StatefulSets provide persistent storage to Pods by associating them with persistent volumes.

### 5. **DaemonSet**
   - **Resource**: DaemonSet
   - **Object**: A specific DaemonSet instance
   - **Definition**: Ensures that a copy of a Pod runs on all (or specific) nodes in the cluster. It’s useful for cluster-wide services, such as monitoring or logging agents, that need to run on each node.

### 6. **Job**
   - **Resource**: Job
   - **Object**: A specific Job instance
   - **Definition**: Creates one or more Pods to complete a specific task. A Job ensures that the task is successfully completed and, once finished, the Pods are terminated.

### 7. **CronJob**
   - **Resource**: CronJob
   - **Object**: A specific CronJob instance
   - **Definition**: Similar to a Job but runs at scheduled times, like cron jobs in Unix/Linux systems. CronJobs allow you to run periodic tasks at fixed times or intervals.

### 8. **Service**
   - **Resource**: Service
   - **Object**: A specific Service instance
   - **Definition**: Exposes a set of Pods as a network service. A Service provides a stable IP and DNS name for accessing Pods, enabling load balancing and allowing Pods to communicate with each other.

### 9. **ConfigMap**
   - **Resource**: ConfigMap
   - **Object**: A specific ConfigMap instance
   - **Definition**: Stores non-sensitive configuration data in key-value pairs that can be used by Pods or other resources within the cluster. It allows you to separate configuration from application code.

### 10. **Secret**
   - **Resource**: Secret
   - **Object**: A specific Secret instance
   - **Definition**: Similar to ConfigMap, but used for storing sensitive data like passwords, OAuth tokens, and SSH keys. Secrets are stored in an encoded format and are accessible only to authorized Pods.

### 11. **Namespace**
   - **Resource**: Namespace
   - **Object**: A specific Namespace instance
   - **Definition**: A way to organize and isolate resources in a Kubernetes cluster. Namespaces help divide a cluster into multiple virtual clusters, providing scope and control over resources.

### 12. **PersistentVolume (PV)**
   - **Resource**: PersistentVolume
   - **Object**: A specific PersistentVolume instance
   - **Definition**: A piece of storage in the cluster provisioned by an administrator or dynamically created via a StorageClass. It provides storage that outlives Pods.

### 13. **PersistentVolumeClaim (PVC)**
   - **Resource**: PersistentVolumeClaim
   - **Object**: A specific PersistentVolumeClaim instance
   - **Definition**: A request for storage resources by a user. A PVC binds to an available PersistentVolume (PV), allowing Pods to use persistent storage.

### 14. **Ingress**
   - **Resource**: Ingress
   - **Object**: A specific Ingress instance
   - **Definition**: Manages external access to services within the cluster, typically HTTP and HTTPS traffic. Ingress can provide routing, SSL termination, and load balancing for web applications.

### 15. **Role**
   - **Resource**: Role
   - **Object**: A specific Role instance
   - **Definition**: Defines a set of permissions (like read, write) within a specific namespace, controlling what actions can be performed on resources in that namespace.

### 16. **ClusterRole**
   - **Resource**: ClusterRole
   - **Object**: A specific ClusterRole instance
   - **Definition**: Similar to a Role, but it defines permissions that apply across the entire cluster, rather than within a specific namespace.

### 17. **PodDisruptionBudget (PDB)**
   - **Resource**: PodDisruptionBudget
   - **Object**: A specific PodDisruptionBudget instance
   - **Definition**: Ensures that a minimum number or percentage of Pods remain available during voluntary disruptions, such as when a node is being drained for maintenance.

### 18. **HorizontalPodAutoscaler (HPA)**
   - **Resource**: HorizontalPodAutoscaler
   - **Object**: A specific HorizontalPodAutoscaler instance
   - **Definition**: Automatically adjusts the number of Pods in a Deployment or ReplicaSet based on observed CPU or memory usage metrics.

---

### Summary:
These are the basic **resources** and **objects** in Kubernetes that you will encounter while managing clusters. Each resource serves a specific purpose for deploying, scaling, networking, and securing applications in a Kubernetes cluster.

 
