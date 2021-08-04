# Security

- High level:

  `Kubectl -> (HTTPS) -> API -> authN -> authZ -> Admission Control(Mutates and validates) -> Schema Validation -> Persisted to etcd.`

- Always disable insecure local ports for Prod.

## Authentication

- Can be set using Bearer tokens, Client certs, Bootstrap tokens and external systems(OpendId connect).

- Kubernetes does not have user resources, the users have to be managed externally.

- Usually users are managed outside k8s in Active Directory, IAM and other services.

- Service accounts are created for system components and accounts are created and managed by cluster and the credentials are stored in K8s secrets.

  - Every component in the cluster gets associated with a service account and that account's identity is used to authenticate requests to the API.

## Authorization

- Usually the authorization modes have Node and RBAC. Node is for kubelets to talk to the cluster.

- RBACs deny everything by default.

- Role based access control(RBAC) usually is -> `Who` can perform which `actions` on which `resources`?

- When we create a cluster, by default we get a context and a user with all permissions. In productions, we need to make Role and RoleBindings to grant permissions to the users.

- RBAC Role:

  ```yml
  kind: ClusterRole
  apiVersion: rbac.authorization.k8s.io/v1
  metadata:
    name: test-rbac
  rules:
  - apiGroups: [""]
    resources: ["pods"]
    verbs: ["get", "list", "watch"]
  ```

- RBAC RoleBinding:

  ```yml
  kind: RoleBinding
  apiVersion: rbac.authorization.k8s.io/v1
  metadata:
    name: test-rbac
  subjects:
  - kind: User
    name: nick
    apiGroup: ""
  roleRef:
    kind: ClusterRole
    name: test-rbac
    apiGroup: ""
  ```

- We can also create similar ClusterRole and ClusterRoleBinding. Usually its better to create Cluster roles once and use them in different RoleBindings for different namespaces.
