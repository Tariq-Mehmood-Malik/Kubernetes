To integrate Calico with Flannel in your Kubernetes cluster, follow these steps:

1. **Download the Canal Manifest:**

   Retrieve the Canal manifest, which combines Flannel and Calico:

   ```bash
   curl https://raw.githubusercontent.com/projectcalico/canal/master/k8s-install/canal.yaml -O
   ```


2. **Modify the Pod CIDR (if necessary):**

   If your cluster uses a different Pod CIDR than `10.244.0.0/16`, edit the `canal.yaml` file to reflect your CIDR:

   ```yaml
   # In canal.yaml, locate the Flannel network configuration section:
   net-conf.json: |
     {
       "Network": "10.244.0.0/16",  # Replace with your Pod CIDR
       "Backend": {
         "Type": "vxlan"
       }
     }
   ```


3. **Apply the Canal Manifest:**

   Deploy Canal to your cluster:

   ```bash
   kubectl apply -f canal.yaml
   ```


4. **Verify the Installation:**

   Check the status of the Canal pods:

   ```bash
   kubectl get pods -n kube-system -l k8s-app=canal
   ```


   Ensure all pods are running without errors.

By following these steps, you integrate Calico's Network Policy enforcement with Flannel's networking capabilities, enhancing your cluster's networking and security features. 
