# Defines our Vagrant environment
#
# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  # create mgmt node
  config.vm.define :mgmt do |mgmt_config|
      mgmt_config.vm.box = "bento/ubuntu-18.04"
      mgmt_config.vm.hostname = "mgmt"
      mgmt_config.vm.network :private_network, ip: "192.168.56.10"
      mgmt_config.vm.provider "virtualbox" do |vb|
        vb.memory = "384"
      end
      mgmt_config.vm.provision :shell, path: "bootstrap-mgmt.sh"
  end

  # create load balancer
  config.vm.define :lb1 do |lb_config|
      lb_config.vm.box = "bento/ubuntu-18.04"
      lb_config.vm.hostname = "lb1"
      lb_config.vm.network :private_network, ip: "192.168.56.11"
      lb_config.vm.network "forwarded_port", guest: 80, host: 8080
      lb_config.vm.provider "virtualbox" do |vb|
        vb.memory = "384"
      end
  end

  # create some web servers
  # https://docs.vagrantup.com/v2/vagrantfile/tips.html
  (1..2).each do |i|
    config.vm.define "web#{i}" do |node|
        node.vm.box = "bento/ubuntu-18.04"
        node.vm.hostname = "web#{i}"
        node.vm.network :private_network, ip: "192.168.56.2#{i}"
        node.vm.network "forwarded_port", guest: 80, host: "808#{i}"
        node.vm.provider "virtualbox" do |vb|
          vb.memory = "384"
        end
    end
  end

end
