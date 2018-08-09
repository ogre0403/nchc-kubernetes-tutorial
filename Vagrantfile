# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/xenial64"
  config.vm.hostname = 'vortex-dev'
  config.vm.define vm_name = 'vortex'

  config.vm.provision "shell", privileged: false, inline: <<-SHELL
    set -e -x -u
    sudo apt-get update
    sudo apt-get install -y vim git cmake build-essential openvswitch-switch tcpdump unzip tig nfs-common

    # Install ntp
    sudo apt-get install -y ntp

    # Install Docker
    # kubernetes official max validated version: 17.03.2~ce-0~ubuntu-xenial
    export DOCKER_VERSION="17.06.2~ce-0~ubuntu"
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
    sudo apt-get update
    sudo apt-get install -y docker-ce=${DOCKER_VERSION}

    # Install Kubernetes
    export KUBE_VERSION="1.11.0"
    export NET_IF_NAME="enp0s8"
    sudo apt-get install -y apt-transport-https curl
    curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
    echo "deb http://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee --append /etc/apt/sources.list.d/kubernetes.list
    sudo apt-get update
    sudo apt-get install -y kubectl kubelet=${KUBE_VERSION}-00 kubeadm=${KUBE_VERSION}-00

    # Disable swap
    sudo swapoff -a && sudo sysctl -w vm.swappiness=0
    sudo sed '/swap.img/d' -i /etc/fstab

    sudo kubeadm init --kubernetes-version v${KUBE_VERSION} --apiserver-advertise-address=172.17.8.101 --pod-network-cidr=10.244.0.0/16
    mkdir -p $HOME/.kube
    sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
    sudo chown $(id -u):$(id -g) $HOME/.kube/config

    # Should give flannel the real network interface name
    wget --quiet https://raw.githubusercontent.com/coreos/flannel/v0.9.1/Documentation/kube-flannel.yml -O /tmp/kube-flannel.yml
    sed -i -- 's/"--kube-subnet-mgr"/"--kube-subnet-mgr", "--iface='"$NET_IF_NAME"'"/g' /tmp/kube-flannel.yml
    kubectl apply -f /tmp/kube-flannel.yml

    kubectl taint nodes --all node-role.kubernetes.io/master-

    git clone https://github.com/hwchiu/kubeDemo
    # Pull the image
    sudo docker pull nginx
    sudo docker pull hwchiu/netutils
  SHELL

  config.vm.network :private_network, ip: "172.17.8.101"
  config.vm.provider :virtualbox do |v|
      v.customize ["modifyvm", :id, "--cpus", 2]
      v.customize ["modifyvm", :id, "--memory", 4096]
      v.customize ['modifyvm', :id, '--nicpromisc1', 'allow-all']
  end
end

