# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  config.vm.box = "centos/7"
  config.vm.provider "virtualbox" do |vb|
    # Customize the amount of memory on the VM:
    vb.memory = "1024"
  end
  config.hostmanager.enabled = true

  # 1台目管理マシン（マシン名：master01）
  config.vm.define "master01" do |master01|
    master01.vm.hostname = "master01"
    master01.vm.network "private_network", ip: "192.168.122.70"
    master01.vm.provision "shell", run: "always", inline: "systemctl restart network.service"
    master01.vm.provision "shell", inline: <<-SHELL
      systemctl stop firewalld.service
      systemctl mask firewalld.service
      yum -y update
      cp /vagrant/kubernetes.repo /etc/yum.repos.d/
      setenforce 0
      yum install -y docker kubelet kubeadm kubectl kubernetes-cni
      systemctl enable docker && systemctl start docker
      systemctl enable kubelet && systemctl start kubelet
      kubeadm init --token 123456.abcdefghijklmnop --api-advertise-addresses 192.168.122.70 --pod-network-cidr=10.244.0.0/16
      kubectl apply -f /vagrant/kube-flannel.yml
    SHELL
  end

  # 2台目 コンテナホスト（マシン名：node01）
  config.vm.define "node01" do |node01|
    node01.vm.hostname = "node01"
    node01.vm.network "private_network", ip: "192.168.122.71"
    node01.vm.provision "shell", run: "always", inline: "systemctl restart network.service"
    node01.vm.provision "shell", inline: <<-SHELL
      systemctl stop firewalld.service
      systemctl mask firewalld.service
      yum -y update
      cp /vagrant/kubernetes.repo /etc/yum.repos.d/
      setenforce 0
      yum install -y docker kubelet kubeadm kubectl kubernetes-cni
      systemctl enable docker && systemctl start docker
      systemctl enable kubelet && systemctl start kubelet
      kubeadm join --token=123456.abcdefghijklmnop master01
    SHELL
  end

  # 3台目 コンテナホスト（マシン名：node02）
  config.vm.define "node02" do |node02|
    node02.vm.hostname = "node02"
    node02.vm.network "private_network", ip: "192.168.122.72"
    node02.vm.provision "shell", run: "always", inline: "systemctl restart network.service"
    node02.vm.provision "shell", inline: <<-SHELL
      systemctl stop firewalld.service
      systemctl mask firewalld.service
      yum -y update
      cp /vagrant/kubernetes.repo /etc/yum.repos.d/
      setenforce 0
      yum install -y docker kubelet kubeadm kubectl kubernetes-cni
      systemctl enable docker && systemctl start docker
      systemctl enable kubelet && systemctl start kubelet
      kubeadm join --token=123456.abcdefghijklmnop master01
    SHELL
  end
end
