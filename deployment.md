# Deployment Manual Scaling

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
