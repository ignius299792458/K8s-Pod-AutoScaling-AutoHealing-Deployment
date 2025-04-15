# Rolling update principle

While we keep updating, edit and changing the configuration files of K8s manifest 
`(pod.yml, deployment.yml, namespace.yml, etc.) `, the ochrestration of k8s remains
operating seamless with other replicas in Running state. 

## Comprehensive Example:

```
➜  echo "Let check all pods state, then do some changes"
Let check all pods state, then do some changes

➜  kubectl get pods -n nginx
NAME                              READY   STATUS    RESTARTS   AGE
nginx-deployment-96b9d695-blhbj   1/1     Running   0          23s
nginx-deployment-96b9d695-cmnr2   1/1     Running   0          10m
nginx-deployment-96b9d695-dxxwq   1/1     Running   0          23s
nginx-deployment-96b9d695-f2w76   1/1     Running   0          95m
nginx-deployment-96b9d695-kdpvg   1/1     Running   0          95m
nginx-deployment-96b9d695-ljzh7   1/1     Running   0          95m
nginx-deployment-96b9d695-rqwhj   1/1     Running   0          95m
nginx-deployment-96b9d695-vt8v7   1/1     Running   0          99m

➜  echo "All the pods are running, So let so wrong changes, see rolling effect.."
All the pods are running, So let so wrong changes, see rolling effect..

➜  kubectl set image deployment/nginx-deployment -n nginx nginx=1.26.2
deployment.apps/nginx-deployment image updated

➜  echo "Here I apply wrong image tag which should be nginx=nginx:1.26.2, see K8s didn't apply this to all pods at once, to maintain zero down time"
Here I apply wrong image tag which should be nginx=nginx:1.26.2, see K8s didn't apply this to all pods at once, to maintain zero down time

➜  kubectl get pods -n nginx
NAME                                READY   STATUS             RESTARTS   AGE
nginx-deployment-6c88fc8949-6xjkc   0/1     ImagePullBackOff   0          93s
nginx-deployment-6c88fc8949-8txw2   0/1     ImagePullBackOff   0          93s
nginx-deployment-6c88fc8949-lxzht   0/1     ImagePullBackOff   0          93s
nginx-deployment-6c88fc8949-v2gbw   0/1     ImagePullBackOff   0          93s
nginx-deployment-96b9d695-cmnr2     1/1     Running            0          13m
nginx-deployment-96b9d695-f2w76     1/1     Running            0          98m
nginx-deployment-96b9d695-kdpvg     1/1     Running            0          98m
nginx-deployment-96b9d695-ljzh7     1/1     Running            0          98m
nginx-deployment-96b9d695-rqwhj     1/1     Running            0          98m
nginx-deployment-96b9d695-vt8v7     1/1     Running            0          102m

➜  echo "There are 3 pods test and other are running and maintaining the availability"
There are 3 pods test and other are running and maintaining the availability

➜  kubectl set image deployment/nginx-deployment -n nginx nginx=nginx:1.26.2
deployment.apps/nginx-deployment image updated

➜  echo "It is correct change, it will applied to all pods"
It is correct change, it will applied to all pods

➜  kubectl get pods -n nginx
NAME                               READY   STATUS              RESTARTS   AGE
nginx-deployment-96b9d695-cmnr2    1/1     Running             0          15m
nginx-deployment-96b9d695-f2w76    1/1     Running             0          100m
nginx-deployment-96b9d695-kdpvg    1/1     Running             0          100m
nginx-deployment-96b9d695-ljzh7    1/1     Running             0          100m
nginx-deployment-96b9d695-rqwhj    1/1     Running             0          100m
nginx-deployment-96b9d695-vt8v7    1/1     Running             0          104m
nginx-deployment-f58b5c699-54ztl   0/1     ContainerCreating   0          32s
nginx-deployment-f58b5c699-g77qh   0/1     ContainerCreating   0          32s
nginx-deployment-f58b5c699-n2mxh   0/1     ContainerCreating   0          32s
nginx-deployment-f58b5c699-rtj8w   0/1     ContainerCreating   0          32s

➜  kubectl get pods -n nginx
NAME                               READY   STATUS              RESTARTS   AGE
nginx-deployment-96b9d695-cmnr2    1/1     Running             0          15m
nginx-deployment-96b9d695-f2w76    1/1     Running             0          100m
nginx-deployment-96b9d695-kdpvg    1/1     Running             0          100m
nginx-deployment-96b9d695-ljzh7    1/1     Running             0          100m
nginx-deployment-96b9d695-rqwhj    1/1     Running             0          100m
nginx-deployment-96b9d695-vt8v7    1/1     Running             0          104m
nginx-deployment-f58b5c699-54ztl   0/1     ContainerCreating   0          35s
nginx-deployment-f58b5c699-g77qh   0/1     ContainerCreating   0          35s
nginx-deployment-f58b5c699-n2mxh   0/1     ContainerCreating   0          35s
nginx-deployment-f58b5c699-rtj8w   0/1     ContainerCreating   0          35s

➜  kubectl get pods -n nginx
NAME                               READY   STATUS              RESTARTS   AGE
nginx-deployment-96b9d695-cmnr2    1/1     Running             0          15m
nginx-deployment-96b9d695-f2w76    1/1     Running             0          100m
nginx-deployment-96b9d695-kdpvg    1/1     Running             0          100m
nginx-deployment-96b9d695-ljzh7    1/1     Running             0          100m
nginx-deployment-96b9d695-rqwhj    1/1     Running             0          100m
nginx-deployment-96b9d695-vt8v7    1/1     Running             0          104m
nginx-deployment-f58b5c699-54ztl   0/1     ContainerCreating   0          37s
nginx-deployment-f58b5c699-g77qh   0/1     ContainerCreating   0          37s
nginx-deployment-f58b5c699-n2mxh   0/1     ContainerCreating   0          37s
nginx-deployment-f58b5c699-rtj8w   0/1     ContainerCreating   0          37s

➜  echo "Its is no Auto healing"
Its is no Auto healing

➜  kubectl get pods -n nginx
NAME                               READY   STATUS    RESTARTS   AGE
nginx-deployment-f58b5c699-2cwjh   1/1     Running   0          4s
nginx-deployment-f58b5c699-54ztl   1/1     Running   0          61s
nginx-deployment-f58b5c699-cm4cm   1/1     Running   0          4s
nginx-deployment-f58b5c699-g77qh   1/1     Running   0          61s
nginx-deployment-f58b5c699-n2mxh   1/1     Running   0          61s
nginx-deployment-f58b5c699-rtj8w   1/1     Running   0          61s
nginx-deployment-f58b5c699-vs7qn   1/1     Running   0          7s
nginx-deployment-f58b5c699-xmpnm   1/1     Running   0          6s


➜  echo "Finally all are at running with new image update..."
Finally all are at running with new image update...

```
