In this step, we will create an nginx service within our cluster.

Kubernetes objects are usually represented in the form of YAML data.

Steps:

1. Check all services currently running in the cluster `kubectl get service`. Think of services as microservices in the cluster.

2. Run `kubectl create -f nginx_service.yaml --save-config` to start the nginx service and check if the new service is created using `kubectl get service`

3. Run `kubectl describe service nginx` to get more info about the service.

4. After changing any configs in the file, you can apply those changes using `kubectl apply -f nginx_service.yaml`

5. Delete a service using `kubectl delete service nginx`
