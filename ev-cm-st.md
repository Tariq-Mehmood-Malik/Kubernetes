# Enviroment Variable, ConfigMap & Secret in K8s

## What are Enviroment Variables

Environment variables are dynamic `key value variables` that are accessible to any process running on the system. The operating system itself will set many environment variables that help running processes understand 
the specifics of the system. Thanks to this, software developers can include logic in their software that makes the programs adjustable to a specific operating system. Environment variables also hold a lot of important 
information about the user. Things like `username`, `preferred language`, `user home directory path`, and many other useful bits of information.   

As a user, you can easily create and access your own environment variables. On Unix-based systems, you can do that by executing the export command followed by the name of your variable and its value. 
So, for example, to create an environment variable called `myvar` with a value of `10`, you need to execute the following:

   ```bash
   export myvar=10
   ```

You can access the value of your defined variable using a dollar sign `$` followed by your variable name. In our case we can print using Linux command `echo`, we can execute the following:   
   ```bash
   echo $myvar
   ```   
And if you want to print all the environment variables, you can execute either **`printenv`** or **`env`** commands. All of this applies to applications running in your pods too.


## Environment Variables in Kubernetes   

The basic principle of environment variables in Kubernetes is the same as above. However, Kubernetes uses environment variables for a few different things. Therefore, it's good to understand what role environment variables play in Kubernetes. 

Before we move any further, you also need to know that it's generally good practice when developing microservices to provide configuration to your Docker containers (in DockerFile) as environment variables whenever possible. 
This way you can make your Docker image more generic and possibly reuse the same image for different purposes.    

In Kubernetes, environment variables are scoped to a container, and there are three main ways of adding them. 

---

## Direct Method

This option is the most straightforward. You can simply specify environment variables directly in your deployment definition with an **env** keyword:   

   ```yaml
   apiVersion: apps/v1
   kind: Deployment
   metadata:
      name: my-app
   spec:
      replicas: 1
      selector:
        matchLabels:
          app: test-app
      template:
        metadata:
          labels:
            app: test-app
        spec:
          containers:
          - name: web-app
            image: nginx:latest
            env:
            - name: ENVIRONMENT
              value: "development"
   ```
   
If your application is instructed to read the value of an environment variable called `ENVIRONMENT`, it will get predefined value to run your application in the desired mode. You can define as much enviroment variables as you like.    

There are some downsides of using the direct method for environment variables in Kubernetes which includes:

1. **Difficult to update**: Changes require updating and redeploying each resource, which can be error-prone and lead to downtime.   
2. **Security risks**: Sensitive values (like passwords or API keys) are exposed in plain text within the YAML files, which can pose security concerns.   

To overcome these issues, we use `ConfigMaps` or `Secrets` in K8s.

---

## ConfigMap   

Another way of providing environment variables to your application is by passing them from Kubernetes `ConfigMap`. A `ConfigMap` allows you to manage environment variables or configuration data separately from the Pod / Deployment configuration (YAML), which is useful for reusable configurations. This way you don't specify the value of the environment variable directly as we did before instead, you instruct Kubernetes to take the value of a specified ConfigMap object and use it as a value of an environment variable.

Lets create a ConfigMap (CM) is K8s with YAML file with 2 Key value pairs.

   ```yaml
   apiVersion: v1
   kind: ConfigMap
   metadata:
     name: my-config
   data:
     MY_ENV_VAR: "Test_value"
     ANOTHER_ENV_VAR: "another_value"
   ```
   
To create the ConfigMap using the YAML file:

   ```bash
   kubectl apply -f configmap.yaml
   ```


We can also create a CM using following command:

   ```bash
   kubectl create configmap my-config --from-literal=MY_ENV_VAR="Test_value" --from-literal=ANOTHER_ENV_VAR="another_value"
   ```


Now lets create a POD which uses above CM for its Enviroment variable.

- 1. You can reference whole CM resource by using `envFrom:` in container specs of POD, which will transfer all key value pairs defined in CM as Enviroment variables to POD. 
   ```yaml
   apiVersion: v1
   kind: Pod
   metadata:
     name: example-pod
   spec:
     containers:
     - name: example-container
       image: nginx
       envFrom:                 # use to refence whole CM
       - configMapRef:
           name: my-config      # name of your CM
   ```

- 2. OR if you only need one key from a CM as an environment variable, you can specify it using the `env` field in the Pod's container spec.
   
   ```yaml
   apiVersion: v1
   kind: Pod
   metadata:
     name: example-pod
   spec:
     containers:
     - name: example-container
       image: nginx
       env:
       - name: MY_ENV_VAR      # Enviroment variable name
         valueFrom:            # Use to specify where to get its value
           configMapKeyRef:
             name: my-config   # CM name
             key: MY_ENV_VAR   # Key name from which we wil get value for our ENV
   ```


- We can also create / get values from file for ConfigMap:
   
   ```bash
   kubectl create configmap my-config --from-file=<path-to-file>
   ```   


- We can also create / get values from Enviroment variables for ConfigMap:
   
   ```bash
   kubectl create configmap my-config --from-env-file=<path-to-env-file>
   ```

- List all ConfigMaps:
   
   ```bash
   kubectl get configmaps
   ```

- Get details of a specific ConfigMap:   
   
   ```bash
   kubectl get configmap my-config -o yaml
   ```

- Update a ConfigMap

   To update an existing ConfigMap, you can either edit it directly or replace it.
   
   Edit a ConfigMap using `kubectl edit`:
   
   ```bash
   kubectl edit configmap my-config
   ```   
   This opens the ConfigMap in an editor where you can make changes.


- Delete a ConfigMap
   
   ```bash
   kubectl delete configmap my-config
   ```



- Mounting ConfigMap as a Volume, we will discuss this in details in K8s Volume section.
   
   ```yaml
   apiVersion: v1
   kind: Pod
   metadata:
     name: example-pod
   spec:
     containers:
     - name: example-container
       image: nginx
       volumeMounts:
       - name: config-volume      # Volume name
         mountPath: /etc/config 
     volumes:
     - name: config-volume
       configMap:
         name: my-config
   ```
   
   This mounts the ConfigMap data as files inside the `/etc/config` directory inside the container.



- Delete All ConfigMaps in a Namespace

   ```bash
   kubectl delete configmap --all -n <namespace>
   ```

---

## Secrets   

Secrets are same as CM but these are used to manage sensitive data such as `passwords`, `API keys`, and `SSH keys`. Secrets are stored in Kubernetes in a way that is more secure than directly putting sensitive data into a ConfigMap or a Pod's environment variables. It is used to securely pass sensitive information to your containers in the form of environment variables or volumes just like of ConfigMap.   

Lets create a Secret using following command:

   ```bash
   kubectl create secret generic my-secret --from-literal=username=admin --from-literal=password=secretpassword
   ```   
   This will create secret with 2 key value pairs.


Now lets create a Secret is K8s with YAML file. Before creating secret in YAMl we need to encode our values in `base64` for security.   
   ```bash
   echo -n "admin" | base64    # Output will be YWRtaW4=
   echo -n "secretpassword" | base64    # Output will be c2VjcmV0cGFzc3dvcmQ=
   ```

   ```yaml
   apiVersion: v1
   kind: Secret
   metadata:
     name: my-secret
   type: Opaque
   data:
     username: YWRtaW4=  # Base64 encoded "admin"
     password: c2VjcmV0cGFzc3dvcmQ=  # Base64 encoded "secretpassword"
   ```

   To create the Secret using the YAML file:

   ```bash
   kubectl apply -f secret.yaml
   ```



Now lets create a POD which uses above CM for its Enviroment variable.

- 1. You can reference whole secret resource by using `envFrom:` in container specs of POD, which will transfer all key value pairs defined in secret as Enviroment variables to POD. 
   ```yaml
   apiVersion: v1
   kind: Pod
   metadata:
     name: example-pod
   spec:
     containers:
     - name: example-container
       image: nginx
       envFrom:                 # use to refence whole Secret
       - secretRef:
           name: my-secret      # name of your Secret
   ```

- 2. OR if you only need one value from a secret as an environment variable, you can specify it using the `env` field in the Pod's container spec.
   
   ```yaml
   apiVersion: v1
   kind: Pod
   metadata:
     name: example-pod
   spec:
     containers:
     - name: example-container
       image: nginx
       env:
       - name: USERNAME
         valueFrom:
           secretKeyRef:
             name: my-secret
             key: username
       - name: PASSWORD
         valueFrom:
           secretKeyRef:
             name: my-secret
             key: password
   ```


- We can also create / get values from file for Secret:
   
   ```bash
   kubectl create secret generic my-secret --from-file=myfile.txt
   ```   


- List all ConfigMaps:
   
   ```bash
   kubectl get secrets
   ```

- Get details of a specific Secret:   
   
   ```bash
   kubectl get secret my-secret -o yaml
   ```
   This will display the Secret in YAML format, but the data will be Base64 encoded.   


- As Volumes
   You can also mount a Secret as a volume in a Pod. This allows your container to access the secret data as files.

   ```yaml
   apiVersion: v1
   kind: Pod
   metadata:
     name: example-pod
   spec:
     containers:
     - name: example-container
       image: nginx
       volumeMounts:
       - name: secret-volume
         mountPath: /etc/secret
     volumes:
     - name: secret-volume
       secret:
         secretName: my-secret
   ```


- Delete a Secret
   
   ```bash
   kubectl delete secret my-secret
   ```

- Update a Secret
   To update a Secret, you need to modify the Secret using either the kubectl command or a YAML file. Kubernetes doesn't support directly updating the data of a Secret.
  However, you can delete and re-create the Secret:
   
   ```bash
   kubectl delete secret my-secret
   kubectl apply -f secret.yaml
   ```

   
- List All Secrets in a Namespace
   ```bash
   kubectl get secrets -n <namespace>
   ```

   
