# Hands-on-03-volume

## emptyDir volume example
```sh
$ kubectl create -f emptyDir-volume.yaml
```

## hostPath volume example
```sh
$ kubectl create -f hostPath-volume.yaml
```

## NFS Volume example

### Install NFS

```sh
# 在 master 執行
$ sudo apt-get update && sudo apt-get install -y nfs-server
$ sudo mkdir -p /nfs/data1
$ sudo mkdir -p /nfs/data2
$ echo "/nfs/data1 *(rw,sync,no_root_squash,no_subtree_check)" | sudo tee -a /etc/exports
$ echo "/nfs/data2 *(rw,sync,no_root_squash,no_subtree_check)" | sudo tee -a /etc/exports
$ sudo /etc/init.d/nfs-kernel-server restart

# 在 node 執行
$ sudo apt-get update && sudo apt-get install -y nfs-common
```
### Create NFS volume Pod example
```sh
$ kubectl create -f nfs-volume.yaml
```

## Persistence Volume Example

```sh
# create PV
$ kubectl create -f persistence-volume/nfs-pv.yaml

# create PVC
$ kubectl create -f persistence-volume/nfs-pvc.yaml

# Use PVC in Pod
$ kubectl create -f persistence-volume/nfs-mount.yaml
```

## Storage Class Example

### Create NFS provider 

Kubernetes 官方storage class 目前並沒有nfs provider, 因此要先手動方裝第三方的external-storage nfs provider. 

```sh
$ kubectl create -f storagc-class/nfs-provider/serviceaccount.yaml
serviceaccounts/nfs-provisioner

$ kubectl create -f storagc-class/nfs-provider/clusterrole.yaml
clusterrole "nfs-provisioner-runner" created

$ kubectl create -f storagc-class/nfs-provider/clusterrolebinding.yaml
clusterrolebinding "run-nfs-provisioner" created

# 可修改`deployment-sa.yaml`裡的`volumes.name[*].path`決定存放的路徑
$ vim storagc-class/nfs-provider/deployment-sa.yaml
...
volumes:
- name: export-volume
    hostPath:
    path: /srv
...

$ kubectl create -f storagc-class/nfs-provider/deployment-sa.yaml
service "nfs-provisioner" created
deployment "nfs-provisioner" created

$ kubectl create -f storagc-class/nfs-provider/storageclass.yaml
storageclass "example-nfs" created
```

### 使用 storage class

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

```sh
# create PVC
$ kubectl create -f storage-class/nfs-pvc.yaml
# Use PVC in Pod
$ kubectl create -f storage-class/nfs-mount.yaml
```

## Cleanup

```sh 
$ kubectl delete pod empty-dir-volume-pod
$ kubectl delete pod host-path-volume-pod
$ kubectl delete pod nfs-volume-pod

$ kubectl delete pv nfs-pv
$ kubectl delete pvc nfs-pvc
$ kubectl delete pod pv-pod

$ kubectl delete deploy nfs-provisioner
$ kubectl delete pod sc-pod
$ kubectl delete pvc nfs-sc-pvc
```