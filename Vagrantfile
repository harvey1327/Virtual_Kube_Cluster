# -*- mode: ruby -*-
# vi: set ft=ruby :

$pkg_update = <<SCRIPT
echo ------------------------------------pkg_update------------------------------------
yum -y update
yum -y install tree
yum -y install vim
SCRIPT

$ansible_install = <<SCRIPT
echo ------------------------------------ansible_install------------------------------------
yum -y install epel-release
yum -y install ansible
SCRIPT

$ssh_config = <<SCRIPT
echo ------------------------------------ssh_config------------------------------------
touch /home/vagrant/.ssh/known_hosts
ssh-keyscan -H 192.168.22.100 >> /home/vagrant/.ssh/known_hosts
ssh-keyscan -H 192.168.22.101 >> /home/vagrant/.ssh/known_hosts
ssh-keyscan -H 192.168.22.102 >> /home/vagrant/.ssh/known_hosts
SCRIPT

Vagrant.configure(2) do |config|

  N = 2
  
  (0..N).reverse_each do |i|
    config.vm.define "node#{i}" do |node|
      node.vm.box = 'centos/7'
      node.vm.hostname = "node#{i}"
      node.ssh.insert_key = false
      node.vm.network :private_network, ip: "192.168.22.10#{i}"
      node.vm.provider :virtualbox do |vb|
        vb.customize ['modifyvm', :id, '--natdnshostresolver1', 'on']
        vb.customize ['modifyvm', :id, '--memory', 1024]
        vb.customize ['modifyvm', :id, '--name', "node#{i}"]
      end
      node.vm.provision :shell, inline: $pkg_update
      node.vm.provision :file, source: '~/.vagrant.d/insecure_private_key', destination: '/home/vagrant/.ssh/id_rsa'
      if i==0
        node.vm.provision :shell, inline: $ansible_install 
        node.vm.provision :shell, inline: $ssh_config
        node.vm.provision 'ansible_local' do |ansible|
          ansible.sudo = true
          ansible.inventory_path = './ansible/inventory.yml'
          ansible.limit = 'all'
          ansible.playbook = './ansible/tooling.yml'
        end
      end
    end
  end

end
