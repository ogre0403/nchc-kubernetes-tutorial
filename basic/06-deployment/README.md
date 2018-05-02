# hands-on-06-deployment

## Create mysql and wordpress deployment

```sh
$ kubectl create -f mysql-deployment.yaml

$ kubectl get deploy
NAME           DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
mysql-deploy   1         1         1            1           12s

$ kubectl get pod -o wide
NAME                            READY     STATUS    RESTARTS   AGE       IP            NODE
mysql-deploy-7cbb695b7b-2ckkr   1/1       Running   0          22s       10.244.0.25   ubuntu

# replace pod ip inside wordpress-deployment.yaml
$ vim wordpress-deployment.yaml
...
    - name: WORDPRESS_DB_HOST
      # replace this value with POD ip
      value: 127.0.0.1
...

$ kubectl create -f wordpress-deployment.yaml

$ kubectl get deploy
NAME                   DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
mysql-deploy           1         1         1            1           1m
wordpress-deployment   3         3         3            0           6s

$  kubectl get pod
NAME                                    READY     STATUS    RESTARTS   AGE
mysql-deploy-7cbb695b7b-2ckkr           1/1       Running   0          1m
wordpress-deployment-7ddfb45745-bcgjt   1/1       Running   0          24s
wordpress-deployment-7ddfb45745-cfv9q   1/1       Running   0          24s
wordpress-deployment-7ddfb45745-rblt9   1/1       Running   0          24s
```

## Scaling

```sh
$ kubectl scale deployment wordpress-deployment  --replicas=4
deployment.extensions "wordpress-deployment" scaled
```

## rolling-update

```sh
# 修改  wordpress-deployment 使用的 image
$ kubectl edit deployment wordpress-deployment
...
    spec:
      containers:
      - image: wordpress:4.9.5-apache
        imagePullPolicy: IfNotPresent
...


# 檢查rolling update的 進度
$ kubectl rollout status deploy  wordpress-deployment
Waiting for rollout to finish: 2 out of 4 new replicas have been updated...
Waiting for rollout to finish: 2 out of 4 new replicas have been updated...
```

## rollback

```sh
# deployment history
$ kubectl rollout history deploy wordpress-deployment 
REVISION  CHANGE-CAUSE
1         <none>
2         <none>
3         <none>

# 回到上一個版本
$ kubectl rollout undo  deploy nginx-deployment

# 回到版本 1 
$ kubectl rollout undo deploy nginx-deployment --to-revision=1
```

## Cleanup

```sh
$ kubectl delete -f mysql-deployment.yaml
$ kubectl delete -f wordpress-deployment.yaml
```