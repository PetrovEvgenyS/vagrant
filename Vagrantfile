# -*- mode: ruby -*-
# vi: set ft=ruby :

BOX_NAME = "almalinux-9.3"

virt_machine = [
  { hostname: "server-vm01", ip: "10.100.10.11", box: BOX_NAME },
  { hostname: "server-vm02", ip: "10.100.10.12", box: BOX_NAME },
  { hostname: "server-vm03", ip: "10.100.10.13", box: BOX_NAME },
]

HOST_SHOW_GUI  = false
HOST_CPUS      = 2
HOST_MEMORY    = 2048
HOST_BRIDGE    = "Hyper-V Virtual Ethernet Adapter #2"
HOST_USER      = "root"
HOST_USER_PASS = "Qq12345"

Vagrant.configure("2") do |config|
  virt_machine.each do |machine|
    config.vm.define machine[:hostname] do |node|
      node.vm.box      = machine[:box]
      node.vm.hostname = machine[:hostname]
      node.vm.network :public_network, bridge: HOST_BRIDGE, ip: machine[:ip]
      node.vm.synced_folder ".", "/vagrant", disabled: true
      node.ssh.username   = HOST_USER
      node.ssh.password   = HOST_USER_PASS
      node.ssh.insert_key = false
      node.vm.provider "virtualbox" do |vm|
        vm.name   = machine[:hostname]
        vm.gui    = HOST_SHOW_GUI
        vm.cpus   = HOST_CPUS
        vm.memory = HOST_MEMORY
      end
    end
  end
end
