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

### Direct Method

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



