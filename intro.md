# Kubernetes

## How we got Kubernetes

Imagine you're a developer trying to deploy an app. Docker comes to the rescue by introducing **containers**, which package your app and all its dependencies into a neat, portable unit that can run anywhere, 
whether on your laptop or in the cloud. This made it much easier to develop and deploy apps consistently across different environments.

As your app grows, you realize you need more than just one container. You need multiple containers—one for the web server, one for the database, and others for different services. Docker provides **Docker Compose**, 
allowing you to manage and run multiple containers together as a single application. This was a big step forward for handling more complex apps.

But soon, the scale of your app increases, and you need to run containers on multiple machines, or **nodes**, to meet growing demand. This is where **Docker Swarm** comes in. 
Swarm helps you manage multiple nodes in a cluster, and it introduces the concept of **replicas**, where you can run multiple copies of a container to handle more traffic. Swarm also provides **load balancing**, automatically distributing traffic to the replicas, ensuring that no single container is overloaded and that your app stays available even if some containers fail.

However, as your app grows even further and becomes more complex, managing and scaling containers manually becomes a challenge. That’s when **Kubernetes** enters the picture. 
Kubernetes builds on the ideas from Docker and Docker Swarm, but with a much more powerful system for **automating container orchestration**. It not only manages containers across many nodes but also offers advanced features like **auto-scaling**, **self-healing**, and better management of complex, multi-container applications.

---
## So, What is Kubernetes?

Kubernetes (K8s) is an open-source platform designed to automate deploying, scaling, and operating application containers, ensuring efficient use of resources, high availability, and load balancing. 
It was originally developed by **Google** and is now maintained by the **Cloud Native Computing Foundation (CNCF)**. Kubernetes has become the go to standard for container orchestration, enabling developers to manage containerized applications across a cluster of machines.

## How Kubernetes Works / Architecture

As we already knows that kubernetes operates in a multi-node infrastructure called `cluster`. A Kubernetes cluster is a set of nodes (physical or virtual machines) that run containerized applications. 
It consists of:
- **Control Plane (Master Node)**
- **Worker Nodes**
 
### Master Node / Control Plane:    
  The master node is where `control plane` components runs, these components are responsible for managing the cluster to achieve the desired state of applications. 
  To ensure high availability there can be multiple master nodes for the `control plane`.  

### Worker Nodes / Data Plane: 
  These nodes are contorlled by control plane and responsible of running the applications, which includes execution of workloads, process requests, and running containers. The group of worker nodes is called `Data Plane`.


Before diving into detailes Master & Worker node componenets lets first understand what is `Pod`:

***Pod:*** As we already know what is container and how container works. Now `Pod` in k8s is nothing more than a wrapper or a "bubble" that can hold one or more containers. It is smallest deployable unit in Kubernetes. 

### Master Node Components:

- **Kube-API Server**
  This is main communuication hub in K8s architecture, it receieves commands / instracututions from CLI through 'kubectl' and comminucates with other components according to recievded instructions.

- **Scheduler**
  When API-Server receive instruction for resource creation it forwards it to Scheduler, sheduler base on worker nodes resources and work loads decides where new resource should be created.

- **Controller Manager**
  
  
