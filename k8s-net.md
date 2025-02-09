# Networks (CDIR) in K8s

In Kubernetes, the network setup is critical to ensuring that various components (such as Pods, Nodes, and Services) can communicate with each other efficiently. 
This is where **CIDR blocks** come into play. Below is a more detailed explanation of each of the primary CIDR blocks used in a Kubernetes cluster and their significance.

## 1. **Pod Network CIDR**   
Defines the IP address range from which `Pods` will be assigned their IP addresses within the Kubernetes cluster. In cluster every Pod is assigned a unique IP from this range, enabling direct communication between Pods, even across different nodes.
The CIDR for `Pods` is provided by administrator during the setup of cluster and is often configured when choosing the network plugin (e.g., Calico, Flannel, Weave). The network plugin is responsible for managing this Pod-to-Pod communication.

#### Example:
**CIDR**: `10.244.0.0/16`       
This means the network can have 65,536 possible Pod IP addresses (from `10.244.0.0` to `10.244.255.255`). The Kubernetes network ensures that traffic is routed correctly.


## 2. **Service Network CIDR**   
Defines the IP range for **Kubernetes Services** (use to expose set of Pods). Services (like LoadBalancers or internal ClusterIP services) get an IP from this range. The Service IP allows clients (like Pods or external users) to access Pods via stable addresses, even if the Pods' IPs change. 
Services provide a **virtual IP (VIP)** that routes traffic to the appropriate backend Pods. A **ClusterIP** service will route traffic to a set of Pods, even if the Pods themselves are restarted or rescheduled to different nodes. Service IPs are used internally within the cluster and are not accessible from outside the cluster unless explicitly exposed.   
The CIDR range for Services is can be defined during the cluster setup (e.g., with the `--service-cluster-ip-range` flag in `kubeadm`) or can be define by k8s iself if not provided.   

#### Example:   
**CIDR**: `10.96.0.0/12`       
This means Kubernetes can have a range of over a million possible Service IPs (from `10.96.0.0` to `10.111.255.255`).


## 3. **Node Network CIDR**   
Specifies the IP range from which the **Node IPs** are assigned, allowing nodes to communicate with each other and the rest of the cluster. Nodes are physical or virtual machines that run your Pods. 
Each node needs to be accessible by other nodes and by the Pods. The Node Network CIDR defines the IPs available for the Nodes. Although Pods have their own CIDR, they can communicate with Nodes (and vice versa) using this network. This helps in scenarios like NodePort services, where traffic from outside the cluster hits a node and is forwarded to the correct Pod.   
This range is usually part of the **underlying infrastructure's networking** (e.g., provided by a cloud provider or manually configured for on-premises clusters).

#### Example:
**CIDR**: `192.168.0.0/16`   
This gives a range of 65,536 possible Node IPs for a cluster.

## 4. **External Network CIDR**   
This refers to the IP ranges used for **external communication** to/from the Kubernetes cluster. This isn't strictly part of the Kubernetes setup itself but is important for integration with external resources.   
- **Ingress/Egress Traffic**: This CIDR range is important when routing traffic to/from the Kubernetes cluster. It can be used for setting up **Ingress controllers**, **LoadBalancers**, or **external access** to Services or Applications running inside the cluster.
- **External Load Balancers**: For instance, if you're using a cloud provider's LoadBalancer (e.g., AWS ELB or GCP LB), the IPs of these LoadBalancers will typically fall under this range.

#### Example:
**CIDR**: `0.0.0.0/0` (this refers to all IP addresses)   
In some cases, it is used in **public IP ranges** to allow traffic from any external network.

## 5. **Pod-to-Service Communication**
Pods communicate with Services via the **ClusterIP** (the virtual IP assigned to Services). For example, a Pod that needs to access a database service can do so by calling the Service's IP (like `10.96.0.10`), and Kubernetes will route traffic to the correct Pods that are associated with that Service.
  
---

- **Pod Network CIDR**: Defines IPs for Pods; enables Pod-to-Pod communication.
- **Service Network CIDR**: Defines IPs for Services; provides stable access to Pods.
- **Node Network CIDR**: Defines IPs for Nodes; enables Node-to-Node communication.
- **External Network CIDR**: Used for routing external traffic to/from the cluster.
- **Routing**: Kubernetes networking ensures that these CIDRs don’t overlap, and traffic is routed properly between Pods, Nodes, and Services.

