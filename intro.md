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

Kubernetes is an open-source platform that automates the deployment, scaling, and management of containerized applications, ensuring efficient use of resources, high availability, and load balancing. 
It simplifies managing containers across multiple machines, making it easier to run and scale large applications automatically.

## How Kubernetes Works / Architecture

As we already knows taht kubernetes operates in a multi-node infrastructure called `cluster`. 
A Kubernetes cluster consists of multiple nodes, which are divided into two types:

**Master Node(s):** The master node is responsible for  running the control plane components, which are responsible for managing the cluster, including tasks like scheduling, scaling, and maintaining the desired state of applications. 
To ensure high availability of cluster, there can be multiple master nodes for the `control plane`.  

**Worker Nodes:** These nodes responsible of running the applications, which includes execution of workloads, process requests, and run containers. The `data plane` is a group of worker nodes.









