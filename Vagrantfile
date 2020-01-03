# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://vagrantcloud.com/search.
  config.vm.box = "centos/7"

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  config.vm.box_check_update = false

  config.vm.define "controller" do |controller|
    controller.vm.hostname = "controller"
    controller.vm.network "private_network", ip: "192.168.200.10"
    controller.vm.provider "VirtualBox" do |vb|
      vb.memory = "2048"
    end
    controller.vm.provision "shell", inline: <<-SHELL
      yum install epel-release -y
      yum install vim git ansible yum-utils device-mapper-persistent-data lvm2 python-devel -y
      yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
      yum install docker-ce docker-ce-cli containerd.io -y
      systemctl enable --now docker
      curl -L "https://github.com/docker/compose/releases/download/1.25.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
      chmod +x /usr/local/bin/docker-compose
      curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
      python get-pip.py
      pip install docker-compose
      git clone https://github.com/ansible/awx.git
      ansible-playbook -i awx/installer/inventory awx/installer/install.yml
      # useradd -m ansible
      echo '192.168.200.10 controller.internal controller' >>/etc/hosts
      echo '192.168.200.20 node1.internal node1'  >>/etc/hosts
      echo '192.168.200.30 node2.internal node2'  >>/etc/hosts
    SHELL
    controller.vm.synced_folder "ansible-scripts", "/home/ansible/ansible-scripts"
  end

  config.vm.define "node1" do |node1|
    node1.vm.hostname = "node1"
    node1.vm.network "private_network", ip: "192.168.200.20"
    node1.vm.provider "VirtualBox" do |vb|
      vb.memory = "512"
    end
    node1.vm.provision "shell", inline: <<-SHELL
      yum install vim -y
      useradd -m ansible 
      echo '192.168.200.10 controller.internal controller' >>/etc/hosts
      echo '192.168.200.20 node1.internal node1'  >>/etc/hosts
      echo '192.168.200.30 node2.internal node2'  >>/etc/hosts
    SHELL

  end

  config.vm.define "node2" do |node2|
    node2.vm.hostname = "node2"
    node2.vm.network "private_network", ip: "192.168.200.30"
    node2.vm.provider "VirtualBox" do |vb|
      vb.memory = "512"
    end
    node2.vm.provision "shell", inline: <<-SHELL
      yum install vim -y
      useradd -m ansible
      echo '192.168.200.10 controller.internal controller' >>/etc/hosts
      echo '192.168.200.20 node1.internal node1'  >>/etc/hosts
      echo '192.168.200.30 node2.internal node2'  >>/etc/hosts
    SHELL

  end


end
