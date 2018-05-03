# Distributed tensorflow in the same pod

```sh
$ kubectl create -f tf-jupyter.yaml

$ kubectl get svc
NAME         TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
kubernetes   ClusterIP   10.96.0.1      <none>        443/TCP          1d
master       NodePort    10.99.97.252   <none>        8888:32186/TCP   8s
```

## Cleanup

```sh
$ kubectl delete -f tf-jupyter.yaml
```