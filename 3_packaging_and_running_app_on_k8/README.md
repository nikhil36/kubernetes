# Running your app on Kubernetes

1. `[Coding Step]` We build the business logic in any language.
1. `[Docker Step]` The code is bundled up in a docker image and stored in a registry.
1. `[K8s Step]` Then a kubernetes object is created which references that image and objects can be of the following types:

    1. Daemon set - Single pod in every node in the cluster.
    2. Deployment - Most common and let's us scale up and down based on demands.
    3. Stateful set - Stateful components in the cluster with auto pod creation.

## Steps

### 1. Creating Docker image

1. Create docker image for the flask app using

    `docker build -t flask-app:latest .`

1. Check the image using -> `docker images`

1. Run the image.

    `docker run -d -p 5000:5000 -it flask-app`

1. Open in a web browser `http://localhost:5000/`

1. Push docker image `docker image push <DOCKER_USER_NAME>/flask-app:latest`

### 2. Create deployment for the Flask-app

1. Apply the deployment using, this will start 3 pods with the image created above.

    `kubectl apply -f flask-deployment.yml`

1. Check the deployment status using

    `kubectl get deploy --watch`

1. Check the pods and get the ip of one pod using

    `kubectl get pods -o wide`

1. Log into a pod using 

    `kubectl exec -it <pod_name> bash`

1. Run the following commands to install `curl`

    `apt update && apt upgrade && apt install curl`

1. Curl using the IP of another pod and check the response.

    `curl 172.17.0.4:5000`


### 2. Create service to route traffic to the app

1. Deploy service using

    `kubectl apply -f web-nodeport.yml`

1. Check service status using

    `kubectl get services`

1. Hit the flask-app using

    `minikube service web-nodeport`

    You should see the output hello docker in the web page.

    `Note`: it is important to make sure app selector labels match for routing of requests. For a cloud provider, we will create a LoadBalancer instead of a NodePort service.
