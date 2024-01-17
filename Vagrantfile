# -*- mode: ruby -*-
# vi: set ft=ruby :

require 'yaml'
settings = YAML.load_file 'provision/config/common.yml'

Vagrant.configure("2") do |config|

  config.vm.box = settings['host_box'] || "cloud06/ariba-ansible-2.4.2"
  config.ssh.username = settings['ariba_user']

  config.vm.define "db" do |db|
    db.vm.hostname = settings['db_hostname']
    db.vm.network "private_network", ip: settings['host_db_address']

    db.vm.provider "vmware_fusion" do |vm|
      vm.vmx["memsize"] = "3072"
    end

    db.vm.provider "virtualbox" do |vb|
      vb.memory = "3072"
    end

    db.vm.provision "ansible_local" do |ansible|
      ansible.playbook = "provision/playbook-db.yml"
      #ansible.galaxy_role_file = "provision/requirements-app.yml"
      #ansible.raw_arguments = ["--module-path", "/vagrant/provision/library/ansible-conda"]
    end

  end

  config.vm.define "app", primary: true do |app|
    app.vm.hostname = settings['ariba_hostname']
    app.vm.network "private_network", ip: settings['host_app_address']
    app.vm.synced_folder "./", "/home/ariba/project"

    app.ssh.forward_agent = true
    app.ssh.forward_x11 = true

    app.vm.provider "vmware_fusion" do |vm|
      # Don't boot with headless mode
      #vm.gui = true
   
      vm.vmx["memsize"] = "4096"
    end

    app.vm.provider "virtualbox" do |vb|
      vb.memory = "4096"
    end

    app.vm.provision "ansible_local" do |ansible|
      ansible.playbook = "provision/playbook-app.yml"
      ansible.galaxy_role_file = "provision/requirements-app.yml"
    end

  end

  config.vm.provision :shell, :inline => "sudo rm /etc/localtime && sudo ln -s /usr/share/zoneinfo/Europe/Paris /etc/localtime", :run => 'always'
end
