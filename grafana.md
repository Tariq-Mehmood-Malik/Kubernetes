To set up MySQL and Grafana on your local Kubernetes cluster, follow these steps:

### 1. **Create a Namespace (Optional)**
   ```bash
   kubectl create namespace monitoring
   ```

### 2. **Set Up MySQL**

#### **Step 2.1: Create a Secret for MySQL Credentials**
```bash
kubectl create secret generic mysql-secret \
  --from-literal=MYSQL_ROOT_PASSWORD=rootpass \
  --from-literal=MYSQL_USER=grafana \
  --from-literal=MYSQL_PASSWORD=grafanapass \
  --namespace=monitoring
```

#### **Step 2.2: Create PersistentVolumeClaim (PVC)**
Save as `mysql-pvc.yaml`:
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mysql-pvc
  namespace: monitoring
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
```
Apply:
```bash
kubectl apply -f mysql-pvc.yaml
```

#### **Step 2.3: Create MySQL Deployment**
Save as `mysql-deployment.yaml`:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mysql-deployment
  namespace: monitoring
spec:
  selector:
    matchLabels:
      app: mysql
  template:
    metadata:
      labels:
        app: mysql
    spec:
      containers:
      - name: mysql
        image: mysql:latest
        env:
        - name: MYSQL_ROOT_PASSWORD
          valueFrom:
            secretKeyRef:
              name: mysql-secret
              key: MYSQL_ROOT_PASSWORD
        - name: MYSQL_USER
          valueFrom:
            secretKeyRef:
              name: mysql-secret
              key: MYSQL_USER
        - name: MYSQL_PASSWORD
          valueFrom:
            secretKeyRef:
              name: mysql-secret
              key: MYSQL_PASSWORD
        - name: MYSQL_DATABASE
          value: mydb
        ports:
        - containerPort: 3306
        volumeMounts:
        - name: mysql-persistent-storage
          mountPath: /var/lib/mysql
      volumes:
      - name: mysql-persistent-storage
        persistentVolumeClaim:
          claimName: mysql-pvc
```
Apply:
```bash
kubectl apply -f mysql-deployment.yaml
```

#### **Step 2.4: Expose MySQL Service**
Save as `mysql-service.yaml`:
```yaml
apiVersion: v1
kind: Service
metadata:
  name: mysql-service
  namespace: monitoring
spec:
  selector:
    app: mysql
  ports:
    - protocol: TCP
      port: 3306
      targetPort: 3306
```
Apply:
```bash
kubectl apply -f mysql-service.yaml
```

### 3. **Initialize MySQL Database**
Port-forward to access MySQL:
```bash
kubectl port-forward svc/mysql-service 3306:3306 -n monitoring
```
In a new terminal, connect and create sample data:
```bash
mysql -u grafana -p -h 127.0.0.1 -P 3306 mydb
```
Enter password `grafanapass` when prompted, then run:
```sql
CREATE TABLE sample_data (
  id INT PRIMARY KEY,
  value INT,
  timestamp DATETIME
);

INSERT INTO sample_data (id, value, timestamp) VALUES
(1, 100, NOW()),
(2, 200, NOW());
```

### 4. **Set Up Grafana**

#### **Step 4.1: Create Grafana Datasource Configuration**
Save as `datasource.yaml`:
```yaml
apiVersion: 1
datasources:
  - name: MySQL
    type: mysql
    url: mysql-service.monitoring.svc.cluster.local:3306
    database: mydb
    user: grafana
    secureJsonData:
      password: grafanapass
    editable: false
```
Create the Secret:
```bash
kubectl create secret generic grafana-datasources \
  --from-file=datasource.yaml \
  --namespace=monitoring
```

#### **Step 4.2: Deploy Grafana**
Save as `grafana-deployment.yaml`:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: grafana-deployment
  namespace: monitoring
spec:
  selector:
    matchLabels:
      app: grafana
  template:
    metadata:
      labels:
        app: grafana
    spec:
      containers:
      - name: grafana
        image: grafana/grafana:latest
        ports:
        - containerPort: 3000
        volumeMounts:
        - name: grafana-datasources
          mountPath: /etc/grafana/provisioning/datasources
          readOnly: true
      volumes:
      - name: grafana-datasources
        secret:
          secretName: grafana-datasources
```
Apply:
```bash
kubectl apply -f grafana-deployment.yaml
```

#### **Step 4.3: Expose Grafana Service**
Save as `grafana-service.yaml`:
```yaml
apiVersion: v1
kind: Service
metadata:
  name: grafana-service
  namespace: monitoring
spec:
  type: NodePort
  selector:
    app: grafana
  ports:
    - protocol: TCP
      port: 3000
      targetPort: 3000
      nodePort: 30001
```
Apply:
```bash
kubectl apply -f grafana-service.yaml
```

### 5. **Access Grafana**
- **Minikube/Docker Desktop**: 
  ```bash
  minikube service grafana-service -n monitoring --url
  ```
  Open the provided URL in your browser.
- **Direct NodePort**: Access via `http://localhost:30001`.

**Login**: Use `admin`/`admin`, then reset the password.

### 6. **Verify Data Source**
1. Go to **Configuration > Data Sources** in Grafana.
2. Check if the MySQL datasource is listed and marked as "OK".

### 7. **Create a Dashboard**
1. Create a new dashboard and add a query using the MySQL datasource.
2. Example query:
   ```sql
   SELECT timestamp as time, value FROM sample_data ORDER BY timestamp
   ```
3. Visualize the data as desired.

### Troubleshooting Tips:
- Check pod statuses: `kubectl get pods -n monitoring`.
- View logs: `kubectl logs <pod-name> -n monitoring`.
- Ensure MySQL is running before Grafana starts.

This setup provides a local Kubernetes environment with MySQL and Grafana, ready for monitoring and visualization.
