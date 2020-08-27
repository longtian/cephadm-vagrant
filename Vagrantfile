# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  config.vm.box = "centos7"
  config.vm.box_url = "https://mirrors.ustc.edu.cn/centos-cloud/centos/7/vagrant/x86_64/images/CentOS-7-x86_64-Vagrant-2004_01.VirtualBox.box"

  config.vm.synced_folder '.', '/vagrant', disabled: true

  Nodes = 3
  (1..Nodes).each do |i|
	config.vm.define "node-#{i}" do |node|
	  node.vm.hostname = "node-#{i}"
	  node.vm.network  "private_network", ip: "192.168.6.#{ 10+i }"
      node.vm.provider "virtualbox" do |v|
        v.memory = 1024
        v.cpus = 1
        unless File.exist?("osd-disks/disk-#{i}.vdi")
          v.customize ["createhd", "--filename", "osd-disks/disk-#{i}.vdi", "--size", 10 * 1024]
          v.customize ["storageattach", :id, "--storagectl", "IDE", "--port", 1, "--device", 0, "--type", "hdd", "--medium", "osd-disks/disk-#{i}.vdi" ]
        end
      end

	  if i == Nodes
	    node.vm.provision "ansible" do |ansible|
	      # Ansible 参数 https://www.vagrantup.com/docs/provisioning/ansible_common
	      ansible.limit = "all"
	      ansible.become = true
	      ansible.playbook = "playbook.yml"
	      ansible.groups = {
	        "mon":["node-1"]
	      }
	    end
	  end
    end
  end
end
