# -*- mode: ruby -*-
# vi: set ft=ruby :
Vagrant.configure("2") do |config|
  config.vm.define "ansible" do |ansible|
    ansible.vm.box = "generic/rocky9"
    ansible.vm.hostname = "ansible"
    ansible.vm.network "private_network", ip: "192.168.56.12"
    ansible.vm.synced_folder ".", "/vagrant", disabled: false
  end

  config.vm.define "server" do |server|
    server.vm.box = "generic/rocky9"
    server.vm.hostname = "server"
    server.vm.network "private_network", ip: "192.168.56.10"
  end

  config.vm.define "client" do |client|
    client.vm.box = "generic/rocky9"
    client.vm.hostname = "client"
    client.vm.network "private_network", ip: "192.168.56.11"
  end
end
