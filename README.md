# Prerequisite
* Ansible Server to run the role(s)
* Master node and Workers nodes installed with Ubuntu 20 (focal)
* Full network connectivity between the Ansible Server, Master node and Workers nodes
  
# Ansible Roles
There are currently **2** roles available:  
  
 ### kubernetes-setup
***Ansible Role to bootstrap 1 Master, multiple Worker nodes Kubernetes Cluster with kubeadm to the Kubernetes version of your choice.***  
*Based on https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/*  
  
This will setup the Kubernetes Cluster of the following design:  
* 1 Master node, >= 1 Worker nodes
* Create a user to manage the Kubernetes Cluster
* Kubernetes version of your choice (eg: 1.20.5-00)
* containerd runtime
* Weave Net for Pod network
* Option of configuring required ports for Master and Worker nodes with [UFW](https://wiki.ubuntu.com/UncomplicatedFirewall)
* Option of creating an NGINX deployment after the Kubernetes Cluster is setup
  
  
### kubernetes-cluster-rolling-updates  
***Ansible Role to perform a rolling upgrade for multiple Master and Worker nodes Kubernetes Cluster to the Kubernetes version of your choice.***  
*Based on https://kubernetes.io/docs/tasks/administer-cluster/kubeadm/kubeadm-upgrade/*  
  
Master node(s) are updated first, then followed by Worker node(s).  
Only 1 node will be down each time for updates.  
Node which is currently upgrading, will be first drained, followed by the Kubernetes components updates.  
This method will ensure that your deployments will not be fully affected/down (provided there are >= 2 worker nodes).  
