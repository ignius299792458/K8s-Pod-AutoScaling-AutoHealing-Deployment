# K8s-Pod-AutoScaling-AutoHealing-Deployment

## `Deployment`
`Definition`: A Deployment provides declarative updates for Pods and ReplicaSets, managing the release of new application versions with features like rollout, rollback, and scaling.
`Example`:
Your company has a customer-facing web application that needs regular updates with zero downtime.
You create a Deployment for your web application with 10 replicas. When your development team releases a new version, you update the Deployment's container image tag. Kubernetes automatically:
- Creates a new ReplicaSet with the updated image
- Gradually scales up the new ReplicaSet (e.g., adding 2 new pods at a time)
- Simultaneously scales down the old ReplicaSet
- Monitors the health of new pods before continuing the rollout

If users report issues with the new version, you can instantly roll back to the previous version with a single command, and Kubernetes reverses the process. During a sales promotion, you can scale the Deployment to 20 replicas to handle increased traffic, and then scale back down afterward.
The Deployment abstracts away the complexity of managing individual pods, making application lifecycle management much simpler for your operations team.

# Kubernetes Workload Resources

Key differences between DaemonSet, StatefulSet, ReplicaSet, and Deployment in Kubernetes:

| Feature | DaemonSet | StatefulSet | ReplicaSet | Deployment |
|---------|-----------|-------------|------------|------------|
| **Purpose** | Runs a pod on every node in the cluster | Maintains state and unique identity for pods | Maintains a stable set of replica pods | High-level resource for deploying and updating applications |
| **Pod Identity** | Each pod is unique to a node | Each pod has persistent identity and stable hostname | Pods are interchangeable | Pods are interchangeable |
| **Scaling** | Automatically scales with nodes (one pod per node) | Manual or auto-scaling with ordered creation/deletion | Manual or auto-scaling with random creation/deletion | Manual or auto-scaling with random creation/deletion |
| **Pod Naming** | Usually `<name>-<node-name>` | `<name>-0`, `<name>-1`, etc. (ordered, sequential) | Random names with hash | Random names with hash |
| **Storage** | Typically uses node storage | Supports stable persistent storage per pod | Shared storage only | Shared storage only |
| **Load Balancing** | Not inherently load-balanced (one per node) | Not automatically load-balanced (identity matters) | Load-balanced (stateless) | Load-balanced (stateless) |
| **Updates** | Rolling updates possible | Ordered, sequential updates | All pods updated simultaneously | Rolling updates, canary deployments, rollbacks |
| **Use Cases** | Monitoring, logging, networking agents | Databases, messaging queues, stateful applications | Rarely used directly (deployment uses it) | Web applications, APIs, stateless workloads |
| **Deletion Order** | Random | Reverse order (highest to lowest index) | Random | Random |
| **Owns ReplicaSet** | No | No | No | Yes (manages ReplicaSets) |


## Deployment Manual Scaling

Example as whole, `create deployment.yml file`, `apply deployment.yml` and `scaling up and down of nginx pod of nginx namespace`

```
➜  kubectl apply -f deployment.yml 
Error from server (BadRequest): error when creating "deployment.yml": Deployment in version "v1" cannot be handled as a Deployment: strict decoding error: unknown field "spec.replica"

➜  vim deployment.yml

➜  kubectl apply -f deployment.yml
deployment.apps/nginx-deployment created

➜  kubectl get deployment -n nginx
NAME               READY   UP-TO-DATE   AVAILABLE   AGE
nginx-deployment   1/2     2            1           19s

➜  kubectl get pods -n nginx
NAME                              READY   STATUS    RESTARTS   AGE
nginx-deployment-96b9d695-vqjkj   1/1     Running   0          50s
nginx-deployment-96b9d695-vt8v7   1/1     Running   0          50s
➜  nginx kubectl scale deployment/nginx-deployment -n nginx --replicas=5
deployment.apps/nginx-deployment scaled


➜  kubectl get pods -n nginx
NAME                              READY   STATUS              RESTARTS   AGE
nginx-deployment-96b9d695-5l6gf   0/1     ContainerCreating   0          12s
nginx-deployment-96b9d695-69rcs   1/1     Running             0          12s
nginx-deployment-96b9d695-rllhp   0/1     ContainerCreating   0          12s
nginx-deployment-96b9d695-vqjkj   1/1     Running             0          114s
nginx-deployment-96b9d695-vt8v7   1/1     Running             0          114s


➜  kubectl get pods -n nginx
NAME                              READY   STATUS              RESTARTS   AGE
nginx-deployment-96b9d695-5l6gf   0/1     ContainerCreating   0          20s
nginx-deployment-96b9d695-69rcs   1/1     Running             0          20s
nginx-deployment-96b9d695-rllhp   0/1     ContainerCreating   0          20s
nginx-deployment-96b9d695-vqjkj   1/1     Running             0          2m2s
nginx-deployment-96b9d695-vt8v7   1/1     Running             0          2m2s

➜  kubectl get pods -n nginx
NAME                              READY   STATUS              RESTARTS   AGE
nginx-deployment-96b9d695-5l6gf   0/1     ContainerCreating   0          21s
nginx-deployment-96b9d695-69rcs   1/1     Running             0          21s
nginx-deployment-96b9d695-rllhp   0/1     ContainerCreating   0          21s
nginx-deployment-96b9d695-vqjkj   1/1     Running             0          2m3s
nginx-deployment-96b9d695-vt8v7   1/1     Running             0          2m3s

➜  kubectl get pods -n nginx
NAME                              READY   STATUS              RESTARTS   AGE
nginx-deployment-96b9d695-5l6gf   0/1     ContainerCreating   0          22s
nginx-deployment-96b9d695-69rcs   1/1     Running             0          22s
nginx-deployment-96b9d695-rllhp   0/1     ContainerCreating   0          22s
nginx-deployment-96b9d695-vqjkj   1/1     Running             0          2m4s
nginx-deployment-96b9d695-vt8v7   1/1     Running             0          2m4s

➜  kubectl scale deployment/nginx-deployment -n nginx --replicas=1
deployment.apps/nginx-deployment scaled

➜  kubectl scale deployment/nginx-deployment -n nginx --replicas=12
deployment.apps/nginx-deployment scaled

➜  kubectl get pods -n nginx
NAME                              READY   STATUS    RESTARTS   AGE
nginx-deployment-96b9d695-2mb2b   1/1     Running   0          11s
nginx-deployment-96b9d695-2st5z   1/1     Running   0          11s
nginx-deployment-96b9d695-f2w76   1/1     Running   0          11s
nginx-deployment-96b9d695-kdpvg   1/1     Running   0          11s
nginx-deployment-96b9d695-kpp4f   1/1     Running   0          11s
nginx-deployment-96b9d695-ljzh7   1/1     Running   0          11s
nginx-deployment-96b9d695-mpwpl   1/1     Running   0          11s
nginx-deployment-96b9d695-rqwhj   1/1     Running   0          11s
nginx-deployment-96b9d695-sdl84   1/1     Running   0          11s
nginx-deployment-96b9d695-t8l6b   1/1     Running   0          11s
nginx-deployment-96b9d695-vt8v7   1/1     Running   0          4m8s
nginx-deployment-96b9d695-vx7ch   1/1     Running   0          11s
➜  nginx kubectl scale deployment/nginx-deployment -n nginx --replicas=5
deployment.apps/nginx-deployment scaled

➜ kubectl get pods -n nginx
NAME                              READY   STATUS    RESTARTS   AGE
nginx-deployment-96b9d695-f2w76   1/1     Running   0          76s
nginx-deployment-96b9d695-kdpvg   1/1     Running   0          76s
nginx-deployment-96b9d695-ljzh7   1/1     Running   0          76s
nginx-deployment-96b9d695-rqwhj   1/1     Running   0          76s
nginx-deployment-96b9d695-vt8v7   1/1     Running   0          5m13s

```
