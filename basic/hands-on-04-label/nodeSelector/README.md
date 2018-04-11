# 如何將 Pod 部署到特定的 Node 上

## 檢查一下目前 Node 有的labels

```sh
$ kubectl get node  --show-labels
NAME           STATUS    ROLES     AGE       VERSION   LABELS
192.168.2.31   Ready     <none>    14d       v1.9.3    beta.kubernetes.io/arch=amd64,beta.kubernetes.io/os=linux,kubernetes.io/hostname=192.168.2.31
```

## 編輯 pod-select-node.yaml

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    app: nginx
spec:
  containers:
  - name: nginx
    image: nginx:1.7.9
    ports:
    - containerPort: 80
  nodeSelector:
    hardware: high-memory
```

## 查看目前 Pod 的狀態

```sh
$ kubectl get pod
NAME                              READY     STATUS    RESTARTS   AGE
nginx                             0/1       Pending   0          21s

$ kubectl describe pod nginx
...
Events:
  Type     Reason            Age               From               Message
  ----     ------            ----              ----               -------
  Warning  FailedScheduling  16s (x8 over 1m)  default-scheduler  0/1 nodes are available: 1 MatchNodeSelector.
```

## 新增一個label到目前的 node 

```sh
# kubectl label node <nodename> <label_name>=<label_value>
$ kubectl label node 192.168.2.31 hardware=high-memory
node "192.168.2.31" labeled

$ kubectl get node --show-labels
NAME       STATUS    ROLES     AGE       VERSION   LABELS
minikube   Ready     <none>    6d        v1.8.0    beta.kubernetes.io/arch=amd64,beta.kubernetes.io/os=linux,hardware=high-memory,kubernetes.io/hostname=minikube
```

## 再次查看目前 Pod 的狀態

```sh
$ kubectl get pod
NAME                              READY     STATUS    RESTARTS   AGE
nginx                             1/1       Running   0          3m

$ kubectl describe pod nginx
...
Events:
  Type     Reason                 Age               From                   Message
  ----     ------                 ----              ----                   -------
  Warning  FailedScheduling       2m (x11 over 4m)  default-scheduler      0/1 nodes are available: 1 MatchNodeSelector.
  Normal   Scheduled              2m                default-scheduler      Successfully assigned nginx to 192.168.2.31
  Normal   SuccessfulMountVolume  2m                kubelet, 192.168.2.31  MountVolume.SetUp succeeded for volume "default-token-k7qqb"
  Normal   Pulling                2m                kubelet, 192.168.2.31  pulling image "nginx:1.7.9"
  Normal   Pulled                 1m                kubelet, 192.168.2.31  Successfully pulled image "nginx:1.7.9"
  Normal   Created                1m                kubelet, 192.168.2.31  Created container
  Normal   Started                1m                kubelet, 192.168.2.31  Started container
```

## Remove label

```sh
# kubectl label node <nodename> <labelname>-
$ kubectl label node 192.168.2.31 hardware-
```