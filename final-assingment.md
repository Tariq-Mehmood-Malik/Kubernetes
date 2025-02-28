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
- Both deployments should be locally available which means we need to create service for each deployemnt with type **`NodePort`** and we need to create Network Policy to ensure that DB pod is only talk with app deployment.    
- Our DB pod should use Volume for its data storage with recycle policy.
- Our web-test deployment should use simple webpage with **env** variable, we will use ConfigMap for index.html as volume and attached it to our web container.
- After that we have to recreate at same setup for **prod** in this step we use multiple K8s features & resources and also follows best practices to make our cluster more optimized and resilliant.

---

# My-Solution:

## Dev Cluster

This is dev setup only for testing creating baseline for production, in production i will enhance each resource to my knowledge.

Lets 1st create a namespace called **dev** for deveploment enviroment for building and testing our cluster.

  ```bash
  kubectl create namespace dev
  ```

f1


Now creating yaml for mongo DB Pod called **db-pod.yaml** as per our requirements, incldung volume for its data. Lets create PV and PVC for our POD.

##### pv1.yaml
```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: recycle-volume
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Recycle
  storageClassName: recycle
  hostPath:
    path: /tmp/pv
```

##### db-pvc.yaml

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: db-pvc
  namespace: dev
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi  
  storageClassName: recycle
```

```bash
mkdir dev
cd dev
nano pv1.yaml
nano db-pvc.yaml
k apply -f pv1.yaml
k apply -f db-pvc.yaml
k get pv
k get pvc -n dev
```

f2


Lets create secret for Mongo DB credentials.


##### db-secret.yaml
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: mongo-credentials
  namespace: dev
type: Opaque
data:
  MONGO_INITDB_ROOT_USERNAME: cm9vdA==  # base64 encoded value of "root"
  MONGO_INITDB_ROOT_PASSWORD: cm9vdHBhc3M=  # base64 encoded value of "rootpass"
  MONGO_INITDB_DATABASE: bXlhcHBkYg==  # base64 encoded value of "myappdb"
```

```bash
nano db-secret.yaml
k apply -f db-secret.yaml
k get secret -n dev
```

f3

Now creating DB POD of mongo. (In prod section we will use StatefulSet for DB pod).

##### db-pod.yaml
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: db-pod
  namespace: dev
  labels:
    type: mongo-db
spec:
  containers:
  - name: db-container
    image: mongo:4.4
    ports:
    - containerPort: 27017
    envFrom:
      - secretRef:
          name: mongo-credentials 
    volumeMounts:
      - mountPath: /data/db
        name: db-data
  volumes:
    - name: db-data
      persistentVolumeClaim:
        claimName: db-pvc
```

```bash
nano db-pod.yaml
k apply -f db-pod.yaml
k get po -n dev
k describe po db-pod -n dev
```


f4

Now lets create a service for our Database through which the our app deployment pods can communicate with DB pod.

##### db-svc.yaml   
```yaml
apiVersion: v1
kind: Service
metadata:
  name: db-svc
  namespace: dev
spec:
  ports:
    - targetPort: 27017
      port: 27017
  selector:
    type: mongo-db
```

```bash
nano db-svc.yaml
k apply -f db-svc.yaml 
k get svc -n dev
k get po -o wide -n dev
```


f5

Now lets create **app** deployment, with env variables to define DB pod service name and pod for easy communication.

##### app-dep.yaml   
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: python-app
  namespace: dev
  labels:
    app: app-dep
spec:
  replicas: 1
  selector:
    matchLabels:
      app: python-app
  template:
    metadata:
      labels:
        app: python-app
    spec:
      containers:
        - name: app-container
          image: python:3.12-alpine
          command: ["tail", "-f", "/dev/null"]
          ports:
            - containerPort: 8080
          env:
            - name: DATABASE_HOST
              value: db-svc
            - name: DATABASE_PORT
              value: "27017"
```

```bash
nano app-dep.yaml 
k apply -f app-dep.yaml 
k get all -n dev
k describe deployment python-app -n dev
```

f6

Now lets create a service type **NodePort** for our deployment app.

##### app-svc.yaml   
```yaml
apiVersion: v1
kind: Service
metadata:
  name: service-app
  namespace: dev
spec:
  type: NodePort
  ports:
    - targetPort: 8080
      port: 8080
      nodePort: 30081
  selector:
    app: python-app
```
```bash
nano app-svc.yaml
k apply -f app-svc.yaml 
k get svc -n dev
```



f7


Now its time to create deployment called **web-test** in yaml file named `web-test.yaml` that should use simple webpage with **env** variables.    

Before creating deployment yaml lets create CM that stores our custom index.html file and mount it as ConfigMap Volume.   

##### index-html.yaml      
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: index-html
  namespace: dev
data:
  index.html: |
    <!DOCTYPE html>
    <html>
    <head>
      <title>DevOps-12</title>
    </head>
    <body>
      <h1>Welcome Students</h1>
      <p>This is Tariq Mehmood</p>
    </body>
    </html>
```   


```bash
nano index-html.yaml
k apply -f index-html.yaml 
k get cm -n dev
```


f8

Now lets create our web-test deployment using nginx.


##### web-dep.yaml   
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-test
  namespace: dev
  labels:
    app: web-dep
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx-webapp
  template:
    metadata:
      labels:
        app: nginx-webapp
    spec:
      containers:
        - name: web-container
          image: nginx
          ports:
            - containerPort: 80
          volumeMounts:
            - name: html-template
              mountPath: /usr/share/nginx/html/index.html
              subPath: index.html
      volumes:
        - name: html-template
          configMap:
            name: index-html
```

Now lets create a service type **NodePort** for our deployment web-test.

##### web-svc.yaml
```yaml
apiVersion: v1
kind: Service
metadata:
  name: service-webapp
  namespace: dev
spec:
  type: NodePort
  ports:
    - targetPort: 80
      port: 80
      nodePort: 30080
  selector:
    app: nginx-webapp
```


Now lets create all objects one by one and test them.

```bash
nano index-html.yaml
k apply -f index-html.yaml 
k get cm -n dev
nano web-dep.yaml 
k apply -f web-dep.yaml 
nano web-svc.yaml 
k apply -f web-svc.yaml 
k get all -n dev
```


f10

---

## Prod

Lets create a Network Policy that allows traffic only from app PODs to DB POD.

##### app-bd-policy.yaml
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: app-to-db
  namespace: prod
spec:
  podSelector:
    matchLabels:
      type: mongo-db
  ingress:
    - from:
        - podSelector:
            matchLabels:
              app: python-app
      ports:
        - protocol: TCP
          port: 27017
```


For the prod setup, best practices include high availability, autoscaling, resource limits, and StatefulSets for MongoDB. This ensures that the prod environment is resilient and optimized for production.

