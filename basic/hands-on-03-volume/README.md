# Hands-on-03-volume

## NFS Volume

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