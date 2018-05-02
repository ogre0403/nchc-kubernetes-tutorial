# hands-on-05-replication-controller

## Create mysql and wordpress replication controller

```sh
$ kubectl create -f mysql-rc.yaml

$ kubectl get pod -o wide
NAME             READY     STATUS    RESTARTS   AGE       IP            NODE
mysql-rc-qrlxt   1/1       Running   0          12s       10.244.0.20   ubuntu

# replace pod ip inside wordpress-rc.yaml
$ vim wordpress-rc.yaml
...
    - name: WORDPRESS_DB_HOST
      # replace this value with POD ip
      value: 127.0.0.1
...

$ kubectl create -f wordpress-rc.yaml

$ kubectl get pod
NAME                 READY     STATUS    RESTARTS   AGE
mysql-rc-qrlxt       1/1       Running   0          5m
wordpress-rc-btxj7   1/1       Running   0          12s
wordpress-rc-gh9r4   1/1       Running   0          12s
wordpress-rc-l996d   1/1       Running   0          12s
```

## Cleanup
```sh
$ kubectl delete -f mysql-rc.yaml
$ kubectl delete -f wordpress-rc.yaml
```
