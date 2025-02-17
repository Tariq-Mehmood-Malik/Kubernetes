# Kubernetes

## How we got Kubernetes

Imagine you're a developer trying to deploy an app. Docker comes to the rescue by introducing containers, which package your app and all its dependencies into a neat, portable unit that can run anywhere whether on your laptop or in the cloud. This makes it much easier to develop and deploy apps consistently across different environments.

As your app grows, you realize you need more than just one container. You need multiple containers—one for the web server, one for the database, and others for different services. Docker provides Docker Compose, a tool that allows you to define and manage multiple containers as a single application using a simple configuration file. This is a big step forward for handling more complex apps, especially during development and testing.

But soon, the scale of your app increases further, and you need to run containers across multiple machines, or nodes, to meet growing demand. This is where Docker Swarm comes in. Docker Swarm plays a key role here, operating on the principle of **container orchestration**. Container orchestration involves the management and automation of deploying, scaling, and running containerized applications across a cluster of machines. Swarm enables you to manage a cluster of nodes and introduces the concept of **replicas**, allowing multiple copies of a container to run and handle increased traffic. Additionally, Swarm includes **built-in load balancing**, which automatically distributes traffic across the replicas. This ensures no single container is overloaded, and your application remains available even if some containers fail.

However, as your app grows even larger and becomes more complex, manually managing and scaling containers becomes a significant challenge. That’s when Kubernetes enters the picture. Kubernetes builds on the ideas introduced by Docker and Docker Swarm but offers a much more powerful and flexible system for automating container orchestration. It not only manages containers across many nodes but also provides advanced features like auto-scaling, self-healing, rolling updates, and storage orchestration. These capabilities make Kubernetes the go-to solution for managing large-scale, production-grade applications with complex requirements.

---
## So, What is Kubernetes?

Kubernetes (K8s) is an open-source platform designed to automate deploying, scaling, and operating application containers, ensuring efficient use of resources, high availability, and load balancing. 
It was originally developed by **Google** and is now maintained by the **Cloud Native Computing Foundation (CNCF)**. Kubernetes has become the go to standard for container orchestration, enabling developers to manage containerized applications across a cluster of machines.

- **Scaling:** Containers are automatically scaled based on demand using horizontal scaling.
- **Operation:** Health checks, self-healing, and monitoring ensure containers run smoothly, with automatic restarts or replacements if issues arise.
- **High Availability:** Replica sets and load balancing ensure that applications stay up and responsive, even if containers or nodes fail.
- **Resource Efficiency:** Resource limits and Kubernetes optimizations ensure efficient use of compute resources.

## How Kubernetes Works / Architecture

As we already knows that kubernetes operates in a multi-node infrastructure called `cluster`. A Kubernetes cluster is a set of nodes (physical or virtual machines) that run containerized applications. 

It consists of:
- **Control Plane (Master Node)**
- **Worker Nodes (Data Plane)**
 
### Master Node / Control Plane:    
  The master node is where `control plane` components runs, these components are responsible for managing the cluster to achieve the desired state of applications. 
  To ensure high availability there can be multiple master nodes for the `control plane`.  

### Worker Nodes / Data Plane: 
  These nodes are contorlled by control plane and responsible of running the applications, which includes execution of workloads, process requests, and running containers. The group of worker nodes is called `Data Plane`.

## Core Components

Before diving into details of Master & Worker node components lets first understand what is `Pod`:

- ***Pod:***   
  We already know what is container and how container works. Now the `Pod` in k8s is nothing more than a wrapper or a "bubble" that can hold one or more containers. In Kubernetes Pod is smallest deployable unit , not individual containers.
    
- ***Why Pod:***   
  K8s uses Pods instead of individual containers because they provide a way to group multiple containers that need to work together, if you are running application container and it need an other container for logging or monitoring in k8s single pod can be use for it. All containers in single Pod share the same network, storage, and resources, making it easy for them to communicate and collaborate. Pods also simplify management because all containers in a Pod start, stop, and scale together as a single unit. This ensures that a Pod of tightly coupled containers are treated as one logical application.

![k8s-arc.svg](images/k8s-arc.svg)


### Master Node Components:

- **Kube-API Server**   
  This is main communuication hub in K8s architecture,  It receives commands/instructions from the CLI (via kubectl) and communicates with other components.

- **Scheduler**   
  When the API Server receives instructions for resource creation such as Pods, it forwards them to the Scheduler. The Scheduler places the Pods onto available worker nodes based on resource availability and workload distribution. It decides where new resources should be created, considering factors like CPU, memory, and other constraints.

- **etcd**   
  After the Scheduler places the Pod on a worker node, the API Server sends the details to etcd. etcd is a distributed key-value store that holds all cluster data and the desired state of the system. It stores configurations, metadata, and runtime state, acting as the "source of truth" for the Kubernetes cluster.
  
- **Controller Manager**   
  The Controller Manager continuously monitors the cluster to ensure it is running in the desired state. It uses information from the API Server and etcd to perform tasks such as maintaining the correct number of Pods, handling node failures, and managing the endpoints for Services. The Controller Manager is responsible for maintaining system health and ensuring that the desired state is consistently met.

- **Cloud Controller Manager (Cloud Only)**   
  It is primarily used when Kubernetes is hosted on a cloud provider. It is a component that allows Kubernetes to interact with the underlying cloud provider's API. It manages cloud-specific resources, such as load balancers, volumes, and networking, and ensures that Kubernetes resources (like nodes and services) are correctly integrated with the cloud infrastructure.

### Worker Node Components:
  
- **Kubelet**   
  Its essential components of each worker node it communicates with the Control Plane (Master Node) to receive instructions and report the status of the node. It ensures that desired containers are running in a Pod as expected. It constantly monitors the Pods assigned to its node, ensuring they are healthy and in the desired state. If a container fails or is terminated, the Kubelet will attempt to restart it to maintain the desired state. It reports the status of the node and its containers back to the Kube-API Server, providing vital information about resource usage, health, and other node metrics.   
  When the Scheduler assigns a Pod to a node, the API Server communicates with the Kubelet on that node to start the necessary containers as specified in the Pod definition. The Kubelet then interacts with the container runtime (like containerd) to launch the containers in the Pod. 

- **Kube-Proxy**   
  Kube-proxy is a network proxy that runs on every worker node in a Kubernetes cluster. It manages network traffic, ensuring that requests to services (Service expose a set of pods as a network service) are properly routed to the correct pods. Since pods in Kubernetes can be ephemeral, constantly destroyed and recreated with changing IP addresses, kube-proxy ensures that services remain accessible and traffic is always routed to the correct set of pods, even as their underlying IPs changes.

- **Container Runtime**   
  A container runtime is the software responsible for running containers on each nodes. It manages the lifecycle of containers including pulling container images, starting and stopping containers.


### **Note:**
 Please note that Master node can also contain componenets like `Kubelet, Kube-proxy & Container Runtime`, now you are wondering why let me explain.      
 When setting up a Kubernetes cluster with **kubeadm** (Which we will cover in upcoming article), control plane components (like the API server and scheduler) are deployed as **static pods**. These pods are managed by the **Kubelet** on each node, not by Kubernetes itself, and are defined in a specific manifest directory. **Kubeadm** automatically creates these static pod definitions to start essential system components. The **Kubelet** ensures these static pods stay up and running, even if they crash or are terminated. Once the API server is running, it manages the cluster's resources, while the **Kubelet** continues to ensure that essential system components, including the API server itself, remain available. The kubelet relies on a **Container Runtime** to execute and run static pods on master nodes. If the master node is also a worker node (not recommended in production), **kube-proxy** is needed. If the master node is only control plane then **kube-proxy** may still run to helps with network management and ensuring traffic can flow to the correct control plane components and services. In many clusters, kube-proxy runs on all nodes (including masters) via a `DaemonSet`.

### K8S ADD-ONS   
Add-ons are additional components or extensions that are installed to enhance the functionality of the cluster. These add-ons provide a variety of features that can help manage networking, storage, monitoring, security, logging, and more. Kubernetes itself is modular and doesn’t include all possible features out of the box, so these add-ons can be added to address many common use cases. We will cover important add-ons in future articles.  


## Kubernetes Resources & Objects

In Kubernetes (K8s), the terms resources and objects are often used interchangeably, but they actually have different meanings. Kubernetes uses a variety of resources and objects to manage applications, networking, and configurations within the cluster. 

- **Kubernetes Resources** are the types (or categories) of components that Kubernetes manages (like Pod, Service, Deployment).   
- **Kubernetes Objects** are the instances or real configurations of these resources (like a specific Pod or Deployment with set parameters).   

In future article we will discuss these resources in details.

## What's Next?

In Next Part, we will create our own K8s Cluster from Scratch.

[Next Article: K8s Cluster Creation →](https://github.com/Tariq-Mehmood-Malik/Kubernetes-Cluster-Creation/blob/main/README.md)
  
  
