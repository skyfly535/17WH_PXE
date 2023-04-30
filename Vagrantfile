# -*- mode: ruby -*-
# vi: set ft=ruby :
# export VAGRANT_EXPERIMENTAL="disks"

Vagrant.configure("2") do |config|

  # config pxeserver
  config.vm.define "pxeserver" do |server|
    server.vm.box = 'bento/centos-8.4' # объявляем название дистрибутива
    server.vm.host_name = 'pxeserver'  # объявляем имя VM
    server.vm.network :private_network, # задаем параметры сети
                       ip: "10.0.0.20", 
                       virtualbox__intnet: 'pxenet'
    
    server.vm.network "forwarded_port", guest: 80, host: 8082 # пробрасываем порт на хостовую машину
    server.vm.network :private_network, ip: "192.168.56.10", adapter: 3 # делаем ещё один сетевой адаптер для Ansible
    server.vm.provider "virtualbox" do |vb|
      vb.memory = "1024" # объем оперативки
      vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"] # включаем режим DNS-прокси с помощью распознавателя хоста
    end
  
  
    server.vm.provision "ansible" do |ansible|  # вызываем playbook для развертывания нужной инфраструктуры на машинах
      ansible.verbose = "v"
      ansible.playbook = "pxe.yml" # файл playbook
    end

  end
  
  
  # config pxeclient
    config.vm.define "pxeclient" do |pxeclient|
      pxeclient.vm.box = 'bento/centos-8.4'  # объявляем название дистрибутива
      pxeclient.vm.host_name = 'pxeclient'   # объявляем имя VM
      pxeclient.vm.network :private_network, ip: "10.0.0.21"  # задаем параметры сети
      pxeclient.vm.provider :virtualbox do |vb|
        vb.memory = "2048" # объем оперативки
        vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
        vb.customize [
            'modifyvm', :id,
            '--nic1', 'intnet',
            '--intnet1', 'pxenet',
            '--nic2', 'nat',
            '--boot1', 'net',
            '--boot2', 'none',
            '--boot3', 'none',
            '--boot4', 'none'
          ]
      vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"] # включаем режим DNS-прокси с помощью распознавателя хоста
      end
  
    end
  
  end