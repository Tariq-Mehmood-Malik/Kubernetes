# Kuberneted Final Assignment

### 1. You need to have a cluster ready with atleast one maste and a worker node. Do you have?

  Yes I have cluster ready, with one master and one worker node, as you can see below.

  1[11](11.png)


### 2.  As a Kubernetes expert, you need to setup a prod and a dev setup

 Lets create 2 namespaces `dev` & `prod`.

```bash
kubectl create namespace dev
kubectl create namespace prod
kubectl get ns
```


3. Dev has 2 Deployments, web-test and app with a DB pod of mongo.

4. 
5. In dev, both deployments are locally available and db is only talk with app
6. Database : Volume is being used for db of recycle one. 
7. For Website, Use a simple webpage of your choice with env variable used (hint : comfigmap and secrets).
8. Replicate same setup for Production setup but it should be resilliant and follow best practices 
9. Suggest some optimization steps.



To deploy two Kubernetes (k8s) deployments—`web-test` and `app`—alongside a MongoDB database pod, follow these steps:

**1. Set Up MongoDB Deployment:**

Begin by creating a MongoDB deployment to serve as your database. It's advisable to use a StatefulSet for MongoDB to ensure stable network identities and persistent storage. Additionally, configure a headless service to facilitate direct communication between MongoDB pods.

*Create a `mongodb-deployment.yaml` file:*

```yaml
apiVersion: v1
kind: Service
metadata:
  name: mongodb
spec:
  ports:
    - port: 27017
  clusterIP: None  # Headless service
  selector:
    app: mongodb
---
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: mongodb
spec:
  serviceName: "mongodb"
  replicas: 3  # Adjust based on your requirements
  selector:
    matchLabels:
      app: mongodb
  template:
    metadata:
      labels:
        app: mongodb
    spec:
      containers:
        - name: mongodb
          image: mongo:latest
          ports:
            - containerPort: 27017
          volumeMounts:
            - name: data
              mountPath: /data/db
      volumes:
        - name: data
          persistentVolumeClaim:
            claimName: mongodb-pvc
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mongodb-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi  # Adjust based on your requirements
```

This configuration sets up a MongoDB StatefulSet with three replicas and a headless service named `mongodb`. Each pod has a persistent volume claim to ensure data persistence.

**2. Deploy the MongoDB Resources:**

Apply the configuration to your Kubernetes cluster:

```bash
kubectl apply -f mongodb-deployment.yaml
```

**3. Set Up the `app` Deployment:**

Next, create the `app` deployment that will interact with the MongoDB database.

*Create an `app-deployment.yaml` file:*

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app
spec:
  replicas: 2  # Adjust based on your requirements
  selector:
    matchLabels:
      app: app
  template:
    metadata:
      labels:
        app: app
    spec:
      containers:
        - name: app
          image: your-app-image:latest  # Replace with your app's image
          ports:
            - containerPort: 8080  # Adjust based on your app's configuration
          env:
            - name: MONGO_URI
              value: "mongodb://mongodb-0.mongodb,mongodb-1.mongodb,mongodb-2.mongodb:27017/your-database"  # Replace with your database name
```

This configuration sets up the `app` deployment with two replicas and configures the environment variable `MONGO_URI` to point to the MongoDB service.

**4. Deploy the `app` Resources:**

Apply the configuration to your Kubernetes cluster:

```bash
kubectl apply -f app-deployment.yaml
```

**5. Set Up the `web-test` Deployment:**

Finally, create the `web-test` deployment, which will serve as the frontend for your application.

*Create a `web-test-deployment.yaml` file:*

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-test
spec:
  replicas: 2  # Adjust based on your requirements
  selector:
    matchLabels:
      app: web-test
  template:
    metadata:
      labels:
        app: web-test
    spec:
      containers:
        - name: web-test
          image: your-web-test-image:latest  # Replace with your web-test image
          ports:
            - containerPort: 80  # Adjust based on your web-test configuration
```

This configuration sets up the `web-test` deployment with two replicas.

**6. Deploy the `web-test` Resources:**

Apply the configuration to your Kubernetes cluster:

```bash
kubectl apply -f web-test-deployment.yaml
```

**7. Expose the Deployments:**

To make your applications accessible, create services for each deployment.

*Create a `services.yaml` file:*

```yaml
apiVersion: v1
kind: Service
metadata:
  name: web-test
spec:
  selector:
    app: web-test
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: app
spec:
  selector:
    app: app
  ports:
    - protocol: TCP
      port: 8080
      targetPort: 8080
```

This configuration creates services for both `web-test` and `app` deployments.

**8. Deploy the Services:**

Apply the configuration to your Kubernetes cluster:

```bash
kubectl apply -f services.yaml
```

**9. Verify the Deployments:**

Check the status of your deployments and services:

```bash
kubectl get deployments
kubectl get services
```

Ensure that all pods are running and that the services are correctly exposed.

**Additional Considerations:**

- **Persistent Storage:** Ensure that your Kubernetes cluster has access to persistent storage to maintain MongoDB data across pod restarts.

- **Environment Variables:** Replace placeholders like `your-app-image:latest`, `your-web-test-image:latest`, and `your-database` with actual values relevant to your application.

- **Scaling:** Adjust the `replicas` count in each deployment based on your application's requirements.

By following these steps, you can deploy the `web-test` and `app` applications alongside a MongoDB database in your Kubernetes cluster. 





To ensure that your MongoDB database communicates exclusively with the `app` deployment in your Kubernetes cluster, you can implement **Network Policies**. Network Policies allow you to control the traffic flow between pods, ensuring that only authorized pods can access specific services.

**1. Prerequisites:**

- **Network Policy Support:** Ensure that your Kubernetes cluster is configured with a network plugin that supports Network Policies, such as Calico, Cilium, or Weave Net. Without this, Network Policies will not be enforced. citeturn0search1

**2. Define the Network Policy:**

Create a Network Policy that allows only the `app` deployment to access the MongoDB service.

*Create a `mongodb-network-policy.yaml` file:*

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-app-to-mongodb
spec:
  podSelector:
    matchLabels:
      app: mongodb
  ingress:
    - from:
        - podSelector:
            matchLabels:
              app: app
      ports:
        - protocol: TCP
          port: 27017
  policyTypes:
    - Ingress
```

**Explanation:**

- **`podSelector`:** Targets the MongoDB pods by their label `app: mongodb`.

- **`ingress`:** Defines the allowed incoming traffic:
  - **`from`:** Allows traffic only from pods labeled `app: app`.
  - **`ports`:** Permits traffic on port `27017`, the default MongoDB port.

- **`policyTypes`:** Specifies that this policy applies to ingress traffic.

**3. Apply the Network Policy:**

Deploy the Network Policy to your Kubernetes cluster:

```bash
kubectl apply -f mongodb-network-policy.yaml
```

**4. Verify the Network Policy:**

To ensure the policy is applied correctly, you can describe it:

```bash
kubectl describe networkpolicy allow-app-to-mongodb
```

**5. Test the Configuration:**

- **Access from `app`:** Verify that the `app` deployment can connect to the MongoDB service.

- **Access from Other Pods:** Ensure that other pods, including the `web-test` deployment, cannot access the MongoDB service.

**Additional Considerations:**

- **Default Deny:** By default, if no Network Policies are applied, all traffic is allowed. Applying a Network Policy with a `podSelector` and no `ingress` or `egress` rules will deny all traffic to the selected pods. citeturn0search1

- **Namespace Isolation:** If your `app` and `mongodb` deployments are in different namespaces, you can specify the `namespaceSelector` in the `from` field to allow traffic from pods in a specific namespace.

- **Testing:** After applying the Network Policy, thoroughly test your applications to ensure that the `app` can access MongoDB and that other pods cannot.

By implementing this Network Policy, you ensure that only the `app` deployment can communicate with the MongoDB database, enhancing the security and isolation of your applications within the Kubernetes cluster. 


