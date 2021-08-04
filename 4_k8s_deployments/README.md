# Deployments

Check [Sample Deployment](sample_redis_deployment.yml)

1. Deployments are higher level objects than pods. Like pods wrap containers, deployments wrap pods in order to provide scaling and other functions.
1. Deployments are defined per pod, meaning we can't multiple containers like web app and database defined in a single deployment yaml. In short, a deployment takes care of running only one type of pod.
1. The scaling operations are managed via replica sets which are managed by the deployment. We don't interact with replica sets, we only manage the deployment. Deployments don't manage scaling and that is managed by replica sets.
1. `Strategy` is used to define how the updates are made in the cluster

    ```yaml
    strategy:
      rollingUpdate:
        maxSurge: 1 # While updating, we can surge max 1 pod from the desired state
        maxUnavailable: 0 # while updating, how many pods can be go below the desired state, 0 means we should never go below the desired state.
      type: RollingUpdate
    ```

    `Note`: During the deployment, a new replica set is created where the new version of pods are deployed and at the same time the pods from the previous replica sets are deleted.

    Always deal with the deployment and never change the replica set.

1. Deployment know which pods it should be managing by using Labels. Pods and deployments are only linked via labels.
1. If a deployment is changed during an ongoing update, then the ongoing update is dropped mid way and the latest deployment change is applied.

## `Sample commands`

1. Deploy service -> `kubectl apply -f sample_redis_deployment.yml`

1. Check deployment -> `kubectl get deploy --watch`

1. Check more info -> `kubectl describe deploy prod-redis`

1. Check replica set -> `kubectl get rs`

1. Now edit the yaml to increase the replica count and then apply changes using step 1 command.

1. We can check the updates took place via command in step 2 and step 4.

1. Now edit the yaml again to trigger a rolling update by modifying the redis version.

1. Now if we check the replica set, we should see another replica set created for the new Redis deployment for rolling update.

1. The rollout status can be checked -> `kubectl rollout status deploy prod-redis`

    We should see an output like -

    `deployment "prod-redis" successfully rolled out`

1. Check the rollout history using -> `kubectl rollout history deploy prod-redis`

1. We should use `--record=true` flag to record the exact command and rolled out changes.

    `kubectl apply -f sample_redis_deployment.yaml --record=true`

1. We can rollback changes using -> `kubectl rollout undo deploy prod-redis`

    Note: Always do rollbacks using yaml changes and never via command line.
