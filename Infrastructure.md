# Kubernetes Infrastructure

Kubernetes is divided into two main parts: **Master Node** and **Worker Node**. Each part plays a crucial role in managing and running applications efficiently.

## 1. Master Node (Control Plane)

The **Master Node** is the control center of the Kubernetes cluster. It is responsible for managing and controlling the entire system. The master node consists of several components that ensure everything is running as expected.

### Key Components of the Master Node:

#### 1.1. API Server (`kube-apiserver`)
The **API Server** is the central point for communication in Kubernetes. It exposes the Kubernetes API, allowing users and components to interact with the cluster.

- **Responsibilities:**
  - Acts as the entry point for all requests (e.g., creating, deleting, or updating resources).
  - Exposes REST API to interact with the cluster.
  - Ensures communication between different Kubernetes components.

---

#### 1.2. Controller Manager (`kube-controller-manager`)
The **Controller Manager** runs various controllers that are responsible for maintaining the desired state of the cluster.

- **Responsibilities:**
  - **Node Controller**: Manages node health.
  - **Replication Controller**: Ensures the desired number of pod replicas are running.
  - **Deployment Controller**: Manages rolling updates for applications.

---

#### 1.3. Scheduler (`kube-scheduler`)
The **Scheduler** decides where to place the pods within the cluster based on resource availability and other constraints.

- **Responsibilities:**
  - Selects worker nodes where pods will run.
  - Considers available resources like CPU, memory, etc.
  - Handles constraints like pod affinity/anti-affinity and resource limits.

---

#### 1.4. Etcd
**Etcd** is a distributed key-value store used by Kubernetes to store cluster data and configuration.

- **Responsibilities:**
  - Stores all cluster data (nodes, pods, deployments, etc.).
  - Acts as the "source of truth" for the state of the cluster.
  - Enables high availability and consistency across the cluster.

---

#### 1.5. Cloud Controller Manager (`cloud-controller-manager`)
The **Cloud Controller Manager** manages integration with external cloud services, such as load balancers, storage, and networking.

- **Responsibilities:**
  - Manages cloud-specific resources (e.g., AWS EC2 instances).
  - Enables Kubernetes to interact with cloud providers like AWS, Azure, or GCP.

---

## 2. Worker Node

The **Worker Node** is where the actual application workloads (pods and containers) run. Each worker node contains several components to ensure proper execution of containers and pods.

### Key Components of the Worker Node:

#### 2.1. Kubelet
The **Kubelet** is an agent running on each worker node that ensures containers are running in their desired state.

- **Responsibilities:**
  - Registers the worker node with the master node.
  - Ensures that containers are running as specified (e.g., restart failed containers).
  - Communicates with the API server to report node and pod status.

---

#### 2.2. Kube Proxy
The **Kube Proxy** manages network traffic and load balancing for services in the Kubernetes cluster.

- **Responsibilities:**
  - Routes external traffic to appropriate pods based on the service.
  - Ensures efficient load balancing and routing.
  - Implements network policies and maintains communication between pods.

---

#### 2.3. Container Runtime
The **Container Runtime** is responsible for running the containers on the worker node. It can be Docker, containerd, or CRI-O.

- **Responsibilities:**
  - Pulls container images from repositories (e.g., Docker Hub).
  - Manages container lifecycle (e.g., start, stop, pause).
  - Creates and runs containers on the node.

---

#### 2.4. Pod
A **Pod** is the smallest deployable unit in Kubernetes. It can contain one or more containers that share networking and storage resources.

- **Responsibilities:**
  - Hosts containers that need to work together and share resources.
  - Ensures communication between containers within the same pod.
  - Manages application and service deployment in the cluster.

---

#### 2.5. CNI (Container Network Interface)
The **CNI** is a specification for setting up networking between containers, ensuring they can communicate with each other and the outside world.

- **Responsibilities:**
  - Assigns network addresses (IP addresses) to pods.
  - Manages network policies (e.g., access control between pods).
  - Ensures communication between pods on different nodes.

---
