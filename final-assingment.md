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
- Our web-test deployment should use simple webpage with **env** variable, we will use env as volume and attached it to our web container.
- After that we have to recreate at same setup for **prod** in this step we use multiple K8s features & resources and also follows best practices to make our cluster more optimized and resilliant.

---

# My-Solution:

## Dev Cluster

Lets 1st create a namespace called **dev** for deveploment enviroment for building and testing our cluster.

  ```bash
  kubectl create namespace dev
  ```

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

Noe creating DB POD of mongo. (In prod section we will use StatefulSet for DB pod).

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
    env:
      - name: MONGO_INITDB_ROOT_USERNAME
        value: "root"
      - name: MONGO_INITDB_ROOT_PASSWORD
        value: "rootpass"
      - name: MONGO_INITDB_DATABASE
        value: "myappdb"
    volumeMounts:
      - mountPath: /data/db
        name: db-data
  volumes:
    - name: db-data
      persistentVolumeClaim:
        claimName: db-pvc
```   

Now lets create a service for our Database through which the app deployment / pods can communicate with DB pod.

##### app-svc.yaml   
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



Now its time to create deployment called **web-test** in yaml file named `web-test.yaml` that should use simple webpage with **env** variables.    

Before creating deployment yaml lets create CM that stores our custom index.html file that uses **env** to mount it as volume and also create an other CM that store our env variables to be used in index.html.   

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
      <title>{{ .Env.PAGE_TITLE }}</title>
    </head>
    <body>
      <h1>{{ .Env.PAGE_HEADER }}</h1>
      <p>{{ .Env.PAGE_MESSAGE }}</p>
    </body>
    </html>
```   

Now lets create another CM that stores env variable values need in index.html file.   

#####  html-data-cm.yaml    
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: html-data
  namespace: dev
data:
  PAGE_TITLE: "DevOps-12"
  PAGE_HEADER: "Welcome Students"
  PAGE_MESSAGE: "This is my web-page"
```


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
          envFrom:
            - configMapRef:
                name: html-data
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
          image: python:3.12-slim-bullseye
          ports:
            - containerPort: 8080
          env:  
            - name: DATABASE_HOST
              value: db-svc
            - name: DATABASE_PORT
              value: "27017"
```

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

Lets create a Network Policy that allows traffic only from app PODs to DB POD.

##### app-bd-policy.yaml
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: app-to-db
  namespace: dev
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


Now lets create all objects one by one and test them.

```bash


```


For the prod setup, best practices include high availability, autoscaling, resource limits, and StatefulSets for MongoDB. This ensures that the prod environment is resilient and optimized for production.

