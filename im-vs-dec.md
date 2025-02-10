# Imperative vs. Declarative Kubernetes

There are two main ways to create these resources in Kubernetes: imperative and declarative methods. Let's break these down in an easy-to-understand way.   

### **1. Imperative Method:**  
The imperative method you tell Kubernetes **exactly what to do** by directly issuing commands. When you use this approach, you are telling Kubernetes exactly what to do, step by step.    
In the imperative approach, you use **kubectl** commands to `create`, `modify`, or `delete` resources. It’s like giving direct instructions to Kubernetes.

In our previous article we discussed what is pod now lets create a pod through imperative method:  
```bash
kubectl run my-pod --image=nginx --port=80
```    
- `kubectl run`: Command to create a Pod.  
- `my-pod`: Name of the Pod.  
- `--image=nginx`: Use the Nginx container image.  
- `--port=80`: Expose port 80.   

**Pros**:  
- `Quick and Direct`: You can create resources quickly without needing a file to define them.   
- `Good for Small Tasks`: If you’re working on something small and simple, imperative commands are fast and effective.

**Cons**:   
- `Not Repeatable`: If you want to recreate the same resource later, you'll have to remember and type out all the commands again. It’s not as reproducible as the declarative approach.   
- `Harder to Manage`: For complex or large-scale systems, imperative commands can get messy and harder to track.  

---

### **2. Declarative Method:**   
The declarative method is different from the imperative method. With this approach, you describe the desired state of your resources and Kubernetes will figure out how to make it happen. 
You don't give exact commands to create resources. Instead, you define what you want the resource to look like.    
In the declarative approach, you define your resources in a `YAML` or `JSON` file. This file describes the resource, such as a `pod`, `deployment`, or `service`, and includes all the necessary configurations. 
Then, you use a command to apply this file to Kubernetes. 

Creating a Pod declaratively (save this as `pod.yaml`):  
```yaml
apiVersion: v1        
kind: Pod             
metadata:
  name: my-pod         
  labels:               
    app: pod-nginx      
spec:                  
  containers:                
  - name: nginx         
    image: nginx:latest      
    ports:
    - containerPort: 80   
 
```
Each YAML in K8s has 4 major parts which are apiVersion, kind, metadata & spec (`AKMS`).   
**`apiVersion`**: Specifies the version of the Kubernetes API for the resource (e.g., `v1` for Pod).   
**`kind`**: Specifies the type of resource (e.g., `Deployment`, `Pod`).   
**`metadata`**: Contains information like the resource's name, namespace, labels, and other identifying data.   
**`spec`**: Defines the desired configuration for the resource, such as container specs, and other settings that describe how Kubernetes should manage the resource.   


Above YAML file tells Kubernetes that you want a Pod resource that should have a container named `nginx`, using the `nginx :latest` image and container port is `80`.    
You would save it and then apply this configuration to Kubernetes using the command:

Then apply it:  
```bash
kubectl apply -f pod.yaml
```
Once you apply this YAML file, Kubernetes will ensure that a pod named **my-pod** is running with the nginx container. If the pod doesn't exist, it will create it. If the pod is already there and doesn't match the definition, Kubernetes will update it to match.

**Pros**:  
- `Version Control`: You can store the YAML files in version control systems (like Git). This makes it easy to track changes and collaborate with others.   
- `Easier to Recreate Resources`: If you want to recreate or change resources, you just update the YAML file and apply it again. Kubernetes will make sure the resource matches the definition.   
- `Consistency`: This method helps maintain consistency, especially in large-scale or production environments. You can define a resource once and be sure that it will always be created the same way.

**Cons**
- `Initial Setup`: You need to create a configuration file (YAML/JSON) for every resource, which takes a bit more effort upfront.   
- `Learning Curve`: Understanding how to write YAML and structure the files can be tricky for beginners.   

---

### **Mixing Both Methods**    
You can use **imperative commands to generate YAML files** For example:  
```bash
kubectl run my-pod --image=nginx --port=80 --dry-run=client -o yaml > pod.yaml
```
This creates a YAML template you can tweak and reuse declaratively.  

---

## What's Next?

In Next Part, we will discuss YAML for Kubernetes.

[Next Article: YAML for Kubernetes →](yaml.md)
   

