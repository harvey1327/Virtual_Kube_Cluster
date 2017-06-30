# -*- mode: ruby -*-
# vi: set ft=ruby :

$pkg_update = <<SCRIPT
echo ------------------------------------pkg_update------------------------------------
yum -y update
yum -y install tree
SCRIPT

$kubectl_install = <<SCRIPT
echo ------------------------------------kubectl_install------------------------------------
curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl
chmod +x ./kubectl
mv ./kubectl /usr/local/bin/kubectl
SCRIPT

$ansible_install = <<SCRIPT
echo ------------------------------------ansible_install------------------------------------
yum -y install epel-release
yum -y install ansible
echo '[Master]' >> /etc/ansible/hosts
echo '192.168.22.100' >> /etc/ansible/hosts
echo '[Node]' >> /etc/ansible/hosts
echo '192.168.22.101' >> /etc/ansible/hosts
echo '192.168.22.102' >> /etc/ansible/hosts
SCRIPT

$ssh_config = <<SCRIPT
echo ------------------------------------ssh_config------------------------------------
touch /home/vagrant/.ssh/known_hosts
ssh-keyscan -H 192.168.22.100 >> /home/vagrant/.ssh/known_hosts
ssh-keyscan -H 192.168.22.101 >> /home/vagrant/.ssh/known_hosts
ssh-keyscan -H 192.168.22.102 >> /home/vagrant/.ssh/known_hosts
SCRIPT

$docker_install = <<SCRIPT
echo ------------------------------------docker_install------------------------------------
yum install -y yum-utils device-mapper-persistent-data lvm2
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
yum install -y docker-ce
systemctl enable docker
usermod -aG docker vagrant
SCRIPT

$kubelet_kubeadm_install = <<SCRIPT
echo ------------------------------------kubelet_kubeadm_install------------------------------------
cat <<EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg
        https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
EOF
setenforce 0
yum install -y kubelet kubeadm
systemctl enable kubelet
SCRIPT

Vagrant.configure(2) do |config|

  config.vm.define 'node1' do |node1|
    node1.vm.box = 'centos/7'
    node1.vm.hostname = 'node1'
    node1.ssh.insert_key = false
    node1.vm.network :private_network, ip: '192.168.22.101'
    node1.vm.provider :virtualbox do |v|
      v.customize ['modifyvm', :id, '--natdnshostresolver1', 'on']
      v.customize ['modifyvm', :id, '--memory', 1024]
      v.customize ['modifyvm', :id, '--name', 'node1']
    end
    node1.vm.provision :shell, inline: $pkg_update
    node1.vm.provision :file, source: '~/.vagrant.d/insecure_private_key', destination: '/home/vagrant/.ssh/id_rsa'
  end

  config.vm.define 'node2' do |node2|
    node2.vm.box = 'centos/7'
    node2.vm.hostname = 'node2'
    node2.ssh.insert_key = false
    node2.vm.network :private_network, ip: '192.168.22.102'
    node2.vm.provider :virtualbox do |v|
      v.customize ['modifyvm', :id, '--natdnshostresolver1', 'on']
      v.customize ['modifyvm', :id, '--memory', 1024]
      v.customize ['modifyvm', :id, '--name', 'node2']
    end
    node2.vm.provision :shell, inline: $pkg_update
    node2.vm.provision :file, source: '~/.vagrant.d/insecure_private_key', destination: '/home/vagrant/.ssh/id_rsa'
  end

  config.vm.define 'master' do |master|
    master.vm.box = 'centos/7'
    master.vm.hostname = 'master'
    master.ssh.insert_key = false
    master.vm.network :private_network, ip: '192.168.22.100'
    master.vm.provider :virtualbox do |v|
      v.customize ['modifyvm', :id, '--natdnshostresolver1', 'on']
      v.customize ['modifyvm', :id, '--memory', 1024]
      v.customize ['modifyvm', :id, '--name', 'master']
    end
    master.vm.provision :shell, inline: $pkg_update
    master.vm.provision :shell, inline: $ansible_install
    master.vm.provision :shell, inline: $ssh_config
    master.vm.provision :file, source: '~/.vagrant.d/insecure_private_key', destination: '/home/vagrant/.ssh/id_rsa'
  end

end
