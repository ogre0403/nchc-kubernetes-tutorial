# NFS Volume

## Install NFS

```sh
# 在 master 執行
$ sudo apt-get update && sudo apt-get install -y nfs-server
$ sudo mkdir /nfs-data
$ echo "/nfs-data *(rw,sync,no_root_squash,no_subtree_check)" | sudo tee -a /etc/exports
$ sudo /etc/init.d/nfs-kernel-server restart

# 在 node 執行
$ sudo apt-get update && sudo apt-get install -y nfs-common
```