# -*- mode: ruby -*-
# vi: set ft=ruby :

# ======== Параметры конфигурации =========
BOX_NAME      = "almalinux-9.3"   # Имя используемого Vagrant box (в данном случае, box локальный)
HOST_SHOW_GUI = false             # false = запуск без GUI VirtualBox
HOST_CPUS     = 2                 # количество выделенных CPU
HOST_MEMORY   = 2048              # размер RAM в МБ
HOST_BRIDGE   = "Hyper-V Virtual Ethernet Adapter #2" # Сетевой bridge-интерфейс хоста
HOST_USER      = "root"           # имя пользователя (УЗ локального Vagrant box)
HOST_USER_PASS = "Qq12345"        # пароль 

# ======== Список ВМ =========
# Задаём имена хостов, статические IP и vagrant box.
virt_machine = [
  { hostname: "server-vm01", ip: "10.100.10.11", box: BOX_NAME },
  { hostname: "server-vm02", ip: "10.100.10.12", box: BOX_NAME },
  { hostname: "server-vm03", ip: "10.100.10.13", box: BOX_NAME },
]

# ======== Конфигурация Vagrant =========
Vagrant.configure("2") do |config|

  # Проход по массиву virt_machine и создание конфигурации для каждой ВМ
  virt_machine.each do |machine|
    config.vm.define machine[:hostname] do |node|

      # Основные параметры ВМ
      node.vm.box      = machine[:box]       # Используемый box
      node.vm.hostname = machine[:hostname]  # Имя хоста
      node.vm.network :public_network, bridge: HOST_BRIDGE, ip: machine[:ip] # Bridge-сеть со статическим IP
      node.vm.synced_folder ".", "/vagrant", disabled: true     # Отключаем синхронизированную папку /vagrant

      # Параметры SSH
      node.ssh.username   = HOST_USER
      node.ssh.password   = HOST_USER_PASS
      node.ssh.insert_key = false

      # Настройки VirtualBox-провайдера
      node.vm.provider "virtualbox" do |vm|
        vm.name   = machine[:hostname]   # Имя ВМ в VirtualBox
        vm.gui    = HOST_SHOW_GUI
        vm.cpus   = HOST_CPUS
        vm.memory = HOST_MEMORY
      end
    end
  end
end
