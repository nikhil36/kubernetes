In this step, we will create an nginx pod within our cluster.
Its a best practice to have one container per pod unless you need more than one.

Kubernetes objects are usually represented in the form of YAML data.

Steps:

1. Check all pod currently running in the cluster `kubectl get pods`

2. Run `kubectl create -f nginx_pod.yaml --save-config` to run the nginx pod and save the config.

3. Run `kubectl get pods` to check if the nginx pod is created.

4. Run `kubectl get pods -o wide` for more detailed info like IP.

5. Check communication to nginx-pod using `curl <IP Address>`.

6. Check logs using `kubectl logs nginx-pod`

7. Delete a service using `kubectl delete pod nginx-pod`
