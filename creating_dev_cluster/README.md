Starting a minikube cluster locally

Ref: https://kubernetes.io/docs/tutorials/

Pre requisite:
1. Docker should be installed locally and kubernetes should be enabled.

Steps:
1. Run `minikube start` to start the cluster.
2. Run `kubectl version` to check verify that the command is working.
3. Run `kubectl cluster-info` to view cluster details.
    >Kubernetes control plane is running at https://127.0.0.1:54229

    >KubeDNS is running at https://127.0.0.1:54229/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

4. Run `kubectl get nodes` -> Show all nodes in the cluster. Locally you should have one node that runs control plane and the master cluster.

5. Once you have a running Kubernetes cluster, you can deploy your containerized applications on top of it. To do so, you create a Kubernetes Deployment configuration. The Deployment instructs Kubernetes how to create and update instances of your application. Once you've created a Deployment, the Kubernetes control plane schedules the application instances included in that Deployment to run on individual Nodes in the cluster.

6. Run this command to create a deployment for the sample app.

    `kubectl create deployment kubernetes-bootcamp --image=gcr.io/google-samples/kubernetes-bootcamp:v1`

7. View deployments via `kubectl get deployments`

8. Check the pods where the boot-camp application is deployed using `kubectl get pods`
    
    - Command to see all kubernetes pods running in kube-system namespace:

        `kubectl get pods -n kube-system`

9. Pods that are running inside Kubernetes are running on a private, isolated network. By default they are visible from other pods and services within the same kubernetes cluster, but not outside that network. When we use kubectl, we're interacting through an API endpoint to communicate with our application.

    Create proxy for 8081 port using :
    1. `kubectl proxy`
    2. On a different terminal, run this command, if it fails then the proxy creation did not work.
    
        `curl http://localhost:8001/version`

    3. Run this command with a pod name to check some info related to a specific pod.

         `curl http://localhost:8001/api/v1/namespaces/default/pods/$POD_NAME`

        Example: `curl http://localhost:8001/api/v1/namespaces/default/pods/    kubernetes-bootcamp-57978f5f5d-tf8qc`

10. To view what containers are inside that Pod and what images are used to build those containers we run the describe pods command:

    `kubectl describe pods`

11. To view logs of a pods, run: 

    `kubectl logs $POD_NAME`

12. We can execute commands directly on the container once the Pod is up and running. For this, we use the `exec` command:

    - Show env variables in a pods - 
    
        `kubectl exec $POD_NAME -- env`

    - Run bash in a pod with interactive terminal, notice the similarity with the same Docker command that runs bash - 
       
        `kubectl exec -it $PODS_NAME bash`