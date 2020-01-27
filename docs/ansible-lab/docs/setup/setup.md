# Overview
The intent of this lab is to provide a gentle introduction to Ansible and provide an environment for further learning and experimentation.  Not all Ansible features are covered, but basics such as ad hoc command execution, playbooks, and templates are introduced.

# Lab Software (Prerequisites)
The lab environment consists of Ubuntu virtual machines powered by VirtualBox and managed by Vagrant.  Neither VirtualBox or Vagrant are covered in depth.  They are used as part of the lab, but full understanding isn't required.

Each of the following should be installed prior to the lab:

* Git and SSH are used for downloading example code and connecting to virtual machines.  Many Linux distributions and macOS provide these tools out of the box.  The [Windows Subsystem for Linux](https://docs.microsoft.com/en-us/windows/wsl/install-win10) and [Git for Windows](https://gitforwindows.org/) are both good options to use Git and SSH in Windows.

* [VirtualBox](https://www.virtualbox.org/wiki/Downloads) is an open source virtualization product.  It runs on Windows, Linux, and Mac and supports most common guest operating systems.

* [Vagrant](https://www.vagrantup.com/downloads.html) is a tool for building and managing virtual machine environments.  A `Vagrantfile` provides a configuration that builds and provisions the multiple VM's in this lab.

    * Understanding of Vagrant isn't necessary, but being familiar with a few basic commands is helpful:
        * `vagrant up`: starts and provisions the Vagrant environment
        * `vagrant ssh <hostname>`: connect to virtual machine with SSH
        * `vagrant halt`: stops virtual machine(s)
        * `vagrant destroy`: terminate and destroy virtual machines
        * `varant help`: get Vagrant help

# Getting Started:
Open shell and change to an appropriate directory.  Clone the git repository and cd into it:
```bash
$ cd ~/Downloads
$ git clone <PLACEHOLDER>
$ cd ansible-lab
```
Use your favorite text editor, less, or more to inspect the `Vagrantfile` and `bootstrap-mgmt.sh` files.  The Vagrantfile (initially) defines four virtual machines.  `web1` and `web2` will be configured as web servers.  `lb1` will be configured as an [HAProxy](http://www.haproxy.org/) software load balancer to distribute traffic to each webserver.  A [control node](https://docs.ansible.com/ansible/latest/user_guide/basic_concepts.html#control-node) called `mgmt` will be configured (or bootstrapped) by Vagrant to automatically preinstall Ansible, copy lab examples, and populate its /etc/hosts file.

It it not necesssary to understand the Vagrant files in order to do the Ansible lab.

# Start the Virtual Machines
From the ansible-lab directory, use Vagrant to start the four virtual machines and connect to the `mgmt` server.
```bash
$ vagrant up
$ vagrant ssh mgmt
```

# Hello Ansible!