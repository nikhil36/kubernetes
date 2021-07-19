## `Kubernetes Networking`

#### Common network requirements these days

1. Highly dynamic networks are the new normal.
2. Constant service discovery is required as apps go up and down .

### `K8s network basic rules`

1. Add nodes in cluster have to be able talk to each other.
2. All pods can talk to each other without NAT.
3. Every pod gets its own IP.
4. Each node gets allocated a subset of IPs and then when a pod gets scheduled to run a node, then its gets an IP from that subset. This IP doesn't change for that pod.
    - All new pods get a different IP, even if a failed pod is replaced with a new pod, then there is a new IP allocated to it.
    - On pod creation, an immutable name and IP is assigned to a pod based on the config. The name and IP get registered with the built-in DNS.
5. The pod network is implemented using CNI plugins but we implement the node network.
6. Every container within the same pod can talk to each other using local host.

### `Service`

- We should not directly rely on pods for handling traffic since they are ephemeral, hence K8s provides a Service interface.
- Services provide a stable network abstraction points to all the pods.
- We create the service layer so that all pods can hit the service instead of going on to podss. This way, we decouple the pods from each other and all the bindings.
- The service also gets its name and IP address registered with the cluster CoreDNS service.
- The service requests are routed by the `kube-proxy` running on each node in IPVS mode, which uses a Linux Kernel IP Virtual Server to route packets to the correct node.
- The label selector is used to route the traffic to pods in the cluster via the service. This is done via the endpoint object created in the cluster that lists all the IPs for the pods that match the label selector defined.

### `Service types`

1. `ClusterIp` : This is the default service type. Each service gets its own IP and is only accessible within the cluster.

2. `NodePort` : They also get their own IP but they enable access from outside by using a cluster wide port.

    For example, you can create a node port service that has IP 192.1.1.0 with node port 80, now if you make a request to an IP of any node in the cluster with the port 80, it will route the request to the NodePort service and the pods that its serving.

    Default port range is from 30000-32767, this can also be changed.

3. `LoadBalancer` : This is for provisioning a cloud based load balancer which routes the traffic from outside using a node port service, which is handled by Kubernetes.

### `Sample commands`

1. Deploy service -> `kubectl apply -f ping_deploy.yml`

2. Check deployment -> `kubectl get deploy`

3. Check IPs using wide view -> `kubectl get pods -o wide`

4. We can also check all the CIDR blocks assigned to each node using the following command. On minikube the result will be different, but on an actual cluster the result will be like a list of CIDR blocks.

    All pods within a node get an IP from the CIDR block of that node.

    `kubectl get nodes -o jsonpath='{.items[*].spec.podCIDR}'`

5. Let's try checking the connectivity between pods using the following commands:

    - `kubectl exec -it <PODS NAME> bash`
    - Update apt-get - `apt-get update`
    - Install networking tools on the base image.

        `apt-get install iputils-ping curl dnsutils iproute2 -y`

    - Check the pods address using - `ip a`
    - Ping other pods using the ip from Step 3 - `ping <pod_id>`

6. Let's try checking the service abstraction using `simple-web.yml`

    - Deploy service -> `kubectl apply -f simple-web.yml`
    - Check the service using -> `kubectl get svc`
    - Log into the ping pods created in Step 5 to run some commands.
    - Curl for service name, this should work since the service name gets registered with the core DNS service.

        `curl hello-svc:8080`

    - Curl for service using the NodePort and the IP of a pod.

        `curl <IP of a node>:30001`

       Note: on minikube, the IP of the node will be cluster IP of minikube.
