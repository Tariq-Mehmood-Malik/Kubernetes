# Kuberneted Final Assignment

As a DevOps Engineer, You are being assigned a task for designing and building Kubernetes infra and resources. You should be using all possible best ways to make it reliable, secure and resilient setup.

1. You need to have a cluster ready with atleast one maste and a worker node. Do you have?
2.  As a Kubernetes expert, you need to setup a prod and a dev setup
3. Dev has 2 Deployments, web-test and app with a DB pod of mongo.
4. In dev, both deployments are locally available and db is only talk with app
5. Database : Volume is being used for db of recycle one. 
6. For Website, Use a simple webpage of your choice with env variable used (hint : comfigmap and secrets).
7. Replicate same setup for Production setup but it should be resilliant and follow best practices 
8. Suggest some optimization steps.

---

### Brain Storming:   

Above is the task assigned to me as **Devops** Engineer. Let's analyze it and then solve it efficiently.    
- 1st we need to setup a K8s Cluster, but i have alreday created the cluster with 1 master & 1 worker node using **Kubeadm** which you can see [here](https://github.com/Tariq-Mehmood-Malik/Kubernetes-Cluster-Creation/blob/main/README.md).
- Now lets focus to next steps, we need to create 2 different namespaces (Virtual Clusters) named **dev** & **prod**.
- Our **dev** cluster should have 2 Deployments named **web-test** & **app** along with a POD of **mongo** database.   
- Both deployments should be locally available which means we need to create service for each deployemnt with type **`NodePort`** and create Network Policy to ensure that DB pod is only talk with app deployment.    
- Our DB pod should use Volume for its data storage with Retain (recycle) policy.
- Our web-test deployment should use simple webpage with **env** variable, we will use env as volume and attached it to our web container.
- After that we have to recreate at same setup for **prod** in this step we use multiple K8s features & resources and also follows best practices to make our cluster more optimized and resilliant.


## My-Solution:

This setup gives you a development cluster with the following components:

Deployments for web-test, app, and mongo.
NodePort services for exposing deployments.
NetworkPolicy to restrict communication between the app and DB pods.
ConfigMap and Secrets for managing environment variables in the web-test pod.
For the prod setup, best practices include high availability, autoscaling, resource limits, and StatefulSets for MongoDB. This ensures that the prod environment is resilient and optimized for production.

