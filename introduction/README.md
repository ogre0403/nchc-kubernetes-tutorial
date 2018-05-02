# Introduction

## Install Docker

```sh
$ sudo apt-get update

# Install packages to allow apt to use a repository over HTTPS
$ sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    software-properties-common

# Add Docker’s official GPG key:
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

# Verify that you now have the key with the fingerprint 
$ sudo apt-key fingerprint 0EBFCD88

# Use the following command to set up the stable repository. 
$ sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"

$ sudo apt-get update

# List the versions available in your repo
$ sudo apt-cache madison docker-ce
docker-ce | 18.03.0~ce-0~ubuntu | https://download.docker.com/linux/ubuntu xenial/stable amd64 Packages

#  Install a specific version  by its fully qualified package name, for example, docker-ce=18.03.0.ce
$ sudo apt-get install docker-ce=18.03.0~ce-0~ubuntu

# Add current user to docker group (using docker without sudo)
$ sudo usermod -aG docker $USER

# Run Hello world Container
$ sudo docker run --rm hello-world
```

## Install Kubernetes

```sh
$ sudo su -

# Install kubelet and kubeadm package
$ apt-get update && apt-get install -y apt-transport-https
$ curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -

$ cat <<EOF >/etc/apt/sources.list.d/kubernetes.list
deb http://apt.kubernetes.io/ kubernetes-xenial main
EOF

$ apt-get update && apt-get install -y kubeadm

# Turn of swap
$ swapoff -a
$ sed -e '/swap/ s/^#*/#/' -i /etc/fstab
$ free -m

# Initializing master
$ kubeadm init --pod-network-cidr=10.244.0.0/16

# Config kubectl
$ mkdir -p $HOME/.kube/
$ sudo cp /etc/kubernetes/admin.conf $HOME/.kube/config
$ sudo chown $(id -u):$(id -g) $HOME/.kube/config

# Enabling shell autocompletion
$ apt-get install -y bash-completion
$ echo "source /etc/bash_completion" >> ~/.bashrc
$ echo "source <(kubectl completion bash)" >> ~/.bashrc
$ source <(kubectl completion bash)

# Enable scheduling pods on the master
$ kubectl taint nodes --all node-role.kubernetes.io/master-

# Installing a pod network
## flannel
$ kubectl apply --namespace kube-system -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml

## or weave
## $ kubectl apply -n kube-system -f \
##    "https://cloud.weave.works/k8s/net?k8s-version=$(kubectl version | base64 | tr -d '\n')"

# Joining your nodes (on workers)
# repeat Install kubelet and kubeadm package
# and    Turn of swap
# on every node
$ kubeadm join --token <token> <master-ip>:<master-port>

# Get nodes information
$ kubectl get nodes
```


## Kubectl usage

### Create

```sh
$ kubectl create -f sample-deploy.yaml
```
### View

```sh
$ kubectl get deployment
$ kubectl get pod
$ kubectl logs <POD-NAME>
$ kubectl describe pod <POD-NAME>
```

### Execute command
```sh
$ kubectl exec -ti <POD-NAME> -- <CMD>
```

### Update
```sh
$ kubectl replace -f /NEW/YAML/FILE
$ kubectl edit deployment <DEPLOYMENT-NAME>
```

### cleanup
```sh
$ kubectl delete deploy nginx-deployment
```


## Reference

1. [Get Docker CE for Ubuntu](https://docs.docker.com/install/linux/docker-ce/ubuntu/)
2. [Using kubeadm to Create a Cluster](https://kubernetes.io/docs/setup/independent/create-cluster-kubeadm/)