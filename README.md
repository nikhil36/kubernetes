# Kubernetes

Kubernetes is a portable, extensible, open-source platform for managing containerized workloads and services, that facilitates both declarative configuration and automation.

Kubernetes basically helps you deploy containers across a pool of compute resources, such as servers.

It provides a networking framework that helps manage and control network communication between containers, provides a variety of different security features that give you the ability to build more secure applications within your Kubernetes infrastructure and it offers some configuration management features that help you manage application configuration and pass configuration data to your containers.

Overview

![Overview](/diagrams/overview.drawio)

# Kubernetes Cluster

A collection of workers machines that run containers.

1. `Control Plane`
    - Control plane manages one or more nodes and is basically a collection of services that controls the cluster.
    - Users interact with the cluster using the control plane and it also monitors the state of the cluster.
    - You can deploy control plane on many servers for high availability.
2. `Node`
    - A node is the smallest unit of computing hardware in Kubernetes. It is a representation of a single machine in your cluster. In most production systems, a node will likely be either a physical machine in a datacenter, or virtual machine hosted on a cloud provider like Google Cloud Platform. Don’t let conventions limit you, however; in theory, you can make a node out of almost anything.
    - A machine that runs containers within the cluster.
    - Manages communication with the control plane for operations and monitoring for the containers running on that node.
    - Worker nodes also use a component called the `kubelet`, which is the Kubernetes agent that runs on each node and manages Kubernetes activity on that node.

3. `Cluster`
    - Although working with individual nodes can be useful, it’s not the Kubernetes way. In general, you should think about the cluster as a whole, instead of worrying about the state of individual nodes.

    - In Kubernetes, nodes pool together their resources to form a more powerful machine. When you deploy programs onto the cluster, it intelligently handles distributing work to the individual nodes for you. If any nodes are added or removed, the cluster will shift around work as necessary. It shouldn’t matter to the program, or the programmer, which individual machines are actually running the code.

4. `Pods`
    - Kubernetes doesn’t run containers directly; instead it wraps one or more containers into a higher-level structure called a `pod`. 
    - Any containers in the same pod will share the same resources and local network. 
    - Containers can easily communicate with other containers in the same pod as though they were on the same machine while maintaining a degree of isolation from others.

More info on: https://medium.com/google-cloud/kubernetes-101-pods-nodes-containers-and-clusters-c1509e409e16