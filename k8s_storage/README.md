## Kubernetes Storage

- The basic idea is to abstract storage and data from pods. With volumes, the data remains in cluster and pod mount that volume, multiple pods can share a volume.

- Volumes simply mean a persistent place to store data.

- We plug in the storage that is provided from the cloud provider or on-premise and Kubernetes provides a Container Storage Interface(CSI plugin) for the pods.

- The speed, replication and resiliency are not managed by Kubernetes, it entirely depends on what you use as a backend storage system.

#### `Container Storage Interface(CSI)`

- Provides a spec that external providers can code to and release updates without relying on Kubernetes releases.
- CSI spec is not just tied to K8s, its an open standard.
<br></br>
#### `Persistent volume subsystem`

- `Persistent volumes(PV)` are storage that is provided by the cloud provider like EBS or persistent disk.

    - We create PV on the cluster and it remains independent like a pod or a node.
 
- `Persistent volume claim` - a pods needs a PVC to in order to use a persistent volume. It also remains in the cluster as an object. Various attributes of PV and PVC should match.
    
    - A PVC automatically creates a PV using the default storage class.

    - We can mount the volume to a pod as a spec manually but its not the best way to do it, check storage classes for more info.

        ```yaml
        spec:
          volumes:
            - name: data
              persistentVolumeClaim:
                claimName: pvc_name
          containers:
            - image: ubuntu:latest
              volumeMounts:
              - mountPath: /data
                name: data         
        ```

- `ACCESS MODES`:

    Typical access modes for a persistent volume:
  
  1. `RWO` Read write once: can only me mounted by one pod for write in the cluster.
  2. `RWM` Read write many: can be mounted by many pods for writing.
  3. `ROM` Read only many: can be mounted by many pods for reading only.

        Note: Not all volumes support all modes and a PV can only have one active PVC access mode at a given time.

- `RECLAIM POLICY`:

    Defines what a cluster does when a claim on the volume is released.

    1. `DELETE`: Deletes volume when a PVC is released.
    2. `RETAIN`: Retains volume, retained volumes have to be deleted manually
    <br></br>
#### `Storage Classes and dynamic provisioning`

- Storage classes enable dynamic provisioning of volumes.
- We create one or more storage classes which map to one provisioner like AWS.
- The parameters in the storage class define what type of storage is provisioned.
- The developer only needs to know the metadata name in order to provision the storage.
- Once a storage class is deployed, anytime a PV claim is made, the SC automatically provisions the storage with the provider.
- If we don't define a storage class in PVC then the default storage class of the cluster is used.
- Sample yaml for provisioning AWS EBS.

    ```yaml
        kind: StorageClass
        apiVersion: storage.k8s.io/v1
        metadata :
          name: fast #Name used in PV claim
        provisioner: kubernetes.io/aws-ebs
        parameters: #Parameters are different for each provisioner
          type: io1
          zones: eu-west-1a
          iopsPerGB: "10"   
    ```

### `Sample commands`

1. Deploy service -> `kubectl apply -f local_pv.yml`

1. Check volumes -> `kubectl get pv`

1. Check IPs using wide view -> `kubectl get pods -o wide`

1. Create PVC using -> `kubectl apply -f local_pvc.yml`

1. Check pvc -> `kubectl get pvc`

1. Create SC using -> `kubectl apply -f local_storage_class.yml`

1. Check pvc -> `kubectl get sc`

1. Describe storage class -> `kubectl describe sc local-sc`


