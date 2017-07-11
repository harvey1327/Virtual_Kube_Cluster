# Virtual_Kube_Cluster

This goal of this vagrant file is to create 1 Master server (defined as node0) and 2 Node servers (defined as node1, node2). On the Master we will use Ansible to create and configure a kubernetes cluster

Run 'vagrant up' to bring up and provision the virtual machines with ansible and basic yum packages.

During the provision, we run ansible to provide tooling for; docker-ce, kubelet and kubeadm.
