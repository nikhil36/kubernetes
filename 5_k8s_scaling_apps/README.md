# Scaling applications

Kubernetes comes with cluster auto scalers for adding more node and horizontal pod autoscaler for adding more pods. There is a vertical pod autoscaler as well but that's fairly new.

- Load increases on the app via open connections or CPU.
- Horizontal pod auto scaler increases the replica count in the deployment objects, this updates the desired state and a new replica set is created and new pods are created. If the cluster is full then the pod goes into pending state, cluster autoscaler keeps checking if there are any pending pods and starts new nodes in the cluster to run those pods.

## Horizontal pod autoscaler(HPA)

- HPA configs are all about pods.
- Demand goes up, new pods are created.
- We define HPA as a cluster object per deployment.
  
  ```yaml
  apiVersion: autoscaling/v1
  kind: HorizontalPodAutoscaler
  metadata:
    name: acg-hpa
    namespace: acg-ns
  spec:
    scaleTargetRef:
      apiVersion: apps/v1
      kind: Deployment
      name: acg-web
    minReplicas: 1 
    maxReplicas: 10 # This defines that the cluster should never have more than 10 pods even if we need to scale more.
    targetCPUUtilizationPercentage: 50 #Scale more pods if CPU is above 50%
  ```

  - We need to create pods with resource requests to use the auto scaler.
  
  ```yml
    spec:
    containers:
    - image: k8s.gcr.io/hpa-example
      name: stresser
      ports:
        - containerPort: 80
      resources:
        limits:
          cpu: 1 # In K8s, 1 virtual CPU in cloud platform is one CPU to K8s
        requests:
          cpu: 0.2 # Al so can be written as 200m
  ```

  - CPU Configs in pods are as

    1 = 1000m = 1 CPU

    0.5 = 500m = 0.5 CPU

    0.2 = 200m = 0.2 CPU

- Autoscaling V1 API can only scale based on CPU utilization but with V2, we can use custom metrics like memory to scale pods.

## Cluster autoscaler

- We build the cluster with auto scaling enabled and define the maximum and minimum number of nodes.
- Cluster autoscaling only works when pods are defined with resource requests.
- Cluster auto scaler work on `requested` values while HPA config works on `actual` metric values like CPU.
- Cluster auto scaler is dependent on cloud platforms.
- Always test cluster performance before going to production.

`Note`: If we have a cluster with 1 node and 1 vCPU, then if have two containers that have requested 0.5 CPU, the cluster autoscaler will spin up new nodes if the app needs to scale to 3 or more container pods.

## `Sample commands`

1. HPA configs are applied using

    `kubectl apply -f hpa_demo.yml`

1. Check HPA status -> `kubectl get hpa --namespace demo-ns`

1. 
