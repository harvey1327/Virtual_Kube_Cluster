# Virtual_Kube_Cluster

This goal of this vagrant file is to create 1 Master server and 2 Node servers. On the Master we will use Ansible to create and configure a kubernetes cluster

Run 'vagrant up' to bring up and provision virtual machines

Run 'vagrant ssh master' to log into master vm

Run 'ansible-playbook -s /home/vagrant/ansible/tooling.yml' to install tooling needed for kubernetes cluster
