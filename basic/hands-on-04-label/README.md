# hands-on-04-label

## Create Mysql and Wordpress Pods

```sh
$ kubectl create -f mysql-pods.yaml

# find out mysql pod ip
$ kubectl get pod -o wide
NAME        READY     STATUS    RESTARTS   AGE       IP            NODE
mysql-pod   1/1       Running   0          29s       10.244.0.17   ubuntu

# replace pod ip inside wordpress-pod.yaml
$ vim wordpress-pod.yaml
...
    - name: WORDPRESS_DB_HOST
      # replace this value with POD ip
      value: 127.0.0.1
...

$ kubectl create -f wordpress-pod.yaml

# show labels
$ kubectl get pod --show-labels
NAME            READY     STATUS    RESTARTS   AGE       LABELS
mysql-pod       1/1       Running   0          11m       app=blog,tier=mysql
wordpress-pod   1/1       Running   0          5m        app=blog,tier=wordpress
```

## NodeSelector: 將 Pod 部署到特定的 Node 上

### 檢查一下目前 Node 有的labels

```sh
$ kubectl get node  --show-labels
NAME      STATUS    ROLES     AGE       VERSION   LABELS
ubuntu    Ready     master    19h       v1.10.2   beta.kubernetes.io/arch=amd64,beta.kubernetes.io/os=linux,kubernetes.io/hostname=ubuntu,node-role.kubernetes.io/master=
```

### 建立一個要特定label的pod

新增 select-node-pod.yaml

```sh
$ kubectl create -f select-node-pod.yaml

$ kubectl get pod
NAME              READY     STATUS    RESTARTS   AGE
mysql-pod         1/1       Running   0          15m
select-node-pod   0/1       Pending   0          7s
wordpress-pod     1/1       Running   0          9m

$ kubectl describe pod select-node-pod
...
Events:
  Type     Reason            Age               From               Message
  ----     ------            ----              ----               -------
  Warning  FailedScheduling  16s (x8 over 1m)  default-scheduler  0/1 nodes are available: 1 node(s) didn't match node selector.
```

### 建立pod所需的label

新增一個label到目前的 node 

```sh
# kubectl label node <nodename> <label_name>=<label_value>
$ kubectl label node ubuntu hardware=high-memory
node "ubuntu" labeled

$ kubectl get node --show-labels
NAME       STATUS    ROLES     AGE       VERSION   LABELS
ubuntu   Ready     <none>    6d        v1.8.0    beta.kubernetes.io/arch=amd64,beta.kubernetes.io/os=linux,hardware=high-memory,kubernetes.io/hostname=ubuntu

# 再次查看目前 Pod 的狀態
$ kubectl describe pod select-node-pod
...
Events:
  Type     Reason                 Age               From               Message
  ----     ------                 ----              ----               -------
  Warning  FailedScheduling       2m (x15 over 5m)  default-scheduler  0/1 nodes are available: 1 node(s) didn't match node selector.
  Normal   Scheduled              1m                default-scheduler  Successfully assigned select-node-pod to ubuntu
  Normal   SuccessfulMountVolume  1m                kubelet, ubuntu    MountVolume.SetUp succeeded for volume "default-token-x6rhw"
  Normal   Pulling                1m                kubelet, ubuntu    pulling image "nginx:1.7.9"
  Normal   Pulled                 41s               kubelet, ubuntu    Successfully pulled image "nginx:1.7.9"
  Normal   Created                41s               kubelet, ubuntu    Created container
  Normal   Started                41s               kubelet, ubuntu    Started container
```

## Cleanup

```sh
$ kubectl delete pod mysql-pod
$ kubectl delete pod wordpress-pod
$ kubectl delete pod select-node-pod
# kubectl label node <nodename> <labelname>-
$ kubectl label node ubuntu hardware-
```