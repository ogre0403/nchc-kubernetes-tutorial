# Storage Class Example

## Install NFS provider

Kubernetes 官方storage class 目前並沒有nfs provider, 因此要先手動方裝第三方的external-storage nfs provider. 

```sh
$ kubectl create -f nfs-provider/serviceaccount.yaml
serviceaccounts/nfs-provisioner
```

```sh
$ kubectl create -f nfs-provider/clusterrole.yaml
clusterrole "nfs-provisioner-runner" created
```

```sh
$ kubectl create -f nfs-provider/clusterrolebinding.yaml
clusterrolebinding "run-nfs-provisioner" created
```

可修改`deployment-sa.yaml`裡的`volumes.name[*].path`決定存放的路徑

```sh
$ vim nfs-provider/deployment-sa.yaml
...
volumes:
- name: export-volume
    hostPath:
    path: /srv
```

```sh
kubectl create -f nfs-provider/deployment-sa.yaml
service "nfs-provisioner" created
deployment "nfs-provisioner" created
```

```sh
$ kubectl create -f class.yaml
storageclass "example-nfs" created
```

## 使用 storage class

在pvc只要宣告要用的storage class，如此一來，就不需要事先建立pv(在此為nfs的pv)，nfs provisioner會自動建立所需要的pv。

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: nfs-pvc
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: "example-nfs"
  resources:
    requests:
      storage: 1Gi
```