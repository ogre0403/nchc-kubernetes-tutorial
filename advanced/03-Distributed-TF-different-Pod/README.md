# Distributed tensorflow in different pod


## Create ps and worker

```sh
$ kubectl create -f tf-ps.yaml
deployment.extensions "ps" created
service "ps-svc" created

$ kubectl create -f tf-worker.yaml
deployment.extensions "worker" created
service "worker-svc" created

$ kubectl get svc
NAME          TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)                         AGE
jupyter-svc   NodePort    10.103.157.120   <none>        8888:32724/TCP                  24s
ps-svc        ClusterIP   10.103.135.173   <none>        2222/TCP                        24s
worker-svc    NodePort    10.111.196.48    <none>        8888:32666/TCP,2222:30277/TCP   27s
```

## Cleanup

```sh
$ kubectl delete -f tf-ps.yaml
$ kubectl delete -f tf-worker.yaml
```