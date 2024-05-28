[![GPL-3.0](https://img.shields.io/badge/license-GPL--3.0-BE0000?style=plastic)](#)  
[![Ubuntu+](https://img.shields.io/badge/Ubuntu-DD4814?style=plastic)](#)  
[![Ansible](https://img.shields.io/badge/Ansible-131211?style=plastic)](#) [![Python 3](https://img.shields.io/badge/Python-3-3673A5?style=plastic)](#)   [![Kubernetes](https://img.shields.io/badge/Kubernetes-326CE5?style=plastic)](#)  
  
# Kubernetes with Ansible
  
  
  
## Introduction
The main goal of this repo is to help with the setup and management of your Kubernetes Cluster using Ansible.
  
## Prerequisites
* Ansible Server to run the role(s)
* Master node and Workers nodes installed with at least Ubuntu 22 or above
* Full network connectivity between the Ansible Server, Master node and Workers nodes
* Ansible inventory file configured, example:  
```
[nfsserver]  
nfs-server 
  
[k8smaster]  
master-node  
  
[k8workers]  
worker-node1  
worker-node2  
  
[k8s:children]  
k8smaster  
k8workers
  
[k8snfs:children]  
nfsserver  
k8smaster  
k8sworkers  
```
* Install the required Ansible collection:  
  `ansible-galaxy install -r requirements.yml`
  
  
  
## Ansible Roles
  
### argocd
  
**Introduction**  
This role is to setup either a HA or single Argo CD server setup with admin password configured and an option to install argocd cli on the master node.
  
**Requirements**  
* FQDN for Argo CD server
* TLS Certificate and Private Key
* Kubernetes Ingress Class
* Argo CD version
  
**Usage**  
1. Update the variables in `roles/argocd/defaults/main.yml`
2. Update the ***hosts*** in `argocd.yml` to Kubernetes Master or Kubernetes Basition host
3. Execute the role: `ansible-playbook argocd.yml`
  
  
### ingress-nginx-metallb
  
**Introduction**  
This role is to setup Ingress NGINX and MetalLB, with the option to test NGINX deployment ingress.
  
**Requirements**  
* MetalLB IP Address Pool
* FQDN for NGINX Deployment
* Ingress NGINX version
* MetalLB version
  
**Usage**  
1. Update the variables in `roles/ingress-nginx-metallb/defaults/main.yml`
2. Update the ***hosts*** in `ingress-nginx-metallb.yml` to Kubernetes Master or Kubernetes Basition host
3. Execute the role: `ansible-playbook ingress-nginx-metallb.yml`
  

### k3s-setup
This role is to bootstrap a k3s Kubernetes Cluster (any k8s verison) consisting of 1 Master node and multiple Worker nodes.  
The container runtime installed will be **containerd** while the container network interface installed will be **calico**.  
*Steps are based on https://docs.k3s.io/installation*  
  
**Requirements**  
* Ansible inventory groups for Kubernetes Master node and Kubernetes Worker nodes
* Whether IPv6 and OS firewall is enabled
* Kubernetes version
* Kubernetes Pod Network CIDR
* Calico Version
  
**Usage**  
1. Update the variables in `roles/k3s-setup/defaults/main.yml`
2. Update the ***hosts*** in `k3s-setup.yml` to Ansible inventory group for Kubernetes Master node and Kubernetes Worker nodes
3. Execute the role: `ansible-playbook k3s-setup.yml`
  
  
### kubernetes-cli-tools
  
**Introduction**  
This role is to install kubextx, kubens, k9s and kube_capacity cli on Kubernetes Basition host. 
  
**Requirements**  
* kubextx version
* kubens version
* k9s version
* kube_capacity version
  
**Usage**  
1. Update the variables in `roles/kubernetes-cli-tools/defaults/main.yml`
2. Update the ***hosts*** in `kubernetes-cli-tools.yml` to Kubernetes Basition host
3. Execute the role: `ansible-playbook kubernetes-cli-tools.yml`
  
  
### kubernetes-setup
This role is to bootstrap a Kubernetes Cluster (any k8s verison) consisting of 1 Master node and multiple Worker nodes with kubeadm.  
The container runtime installed will be **containerd** while the container network interface installed will be **calico**.  
*Steps are based on https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/*  
  
**Requirements**  
* Ansible inventory groups for Kubernetes Master node and Kubernetes Worker nodes
* Whether IPv6 and OS firewall is enabled
* Kubernetes API Server Port
* Kubernetes version
* Kubernetes Pod Network CIDR
* Calico Version
  
**Usage**  
1. Update the variables in `roles/kubernetes-setup/defaults/main.yml`
2. Update the ***hosts*** in `kubernetes-setup.yml` to Ansible inventory group for Kubernetes Master node and Kubernetes Worker nodes
3. Execute the role: `ansible-playbook kubernetes-setup.yml`
  
  
### nfs-subdir-external-provisioner
  
**Introduction**  
This role is to setup NFS Subdir External Provisioner storage class for the Kubernetes cluster.
  
**Requirements**  
* Ansible inventory groups for Kubernetes Master node, Kubernetes Worker nodes and NFS server
* NFS Shares
* NFS Network CIDR to whitelist
* Testing of mounting NFS share on Kubernetes Worker nodes
* Namespace(s) to setup NFS Subdir External Provisioner deployment
  
**Usage**  
1. Update the variables in `roles/nfs-subdir-external-provisioner/defaults/main.yml`
2. Update the ***hosts*** in `nfs-subdir-external-provisioner.yml` to Ansible inventory group for Kubernetes Master node, Kubernetes Worker nodes and NFS server 
3. Execute the role: `ansible-playbook nfs-subdir-external-provisioner.yml`
  
### kubernetes-cluster-rolling-updates  
***Ansible Role to perform a rolling upgrade for multiple Master and Worker nodes Kubernetes Cluster to the Kubernetes version of your choice.***  
*Based on https://kubernetes.io/docs/tasks/administer-cluster/kubeadm/kubeadm-upgrade/*  
  
Master node(s) are updated first, then followed by Worker node(s).  
Only 1 node will be down each time for updates.  
Node which is currently upgrading, will be first drained, followed by the Kubernetes components updates.  
This method will ensure that your deployments will not be fully affected/down (provided there are >= 2 worker nodes).  
#### Usage:  
1. Download both *kubernetes-cluster-rolling-updates.yml* file and *kubernetes-cluster-rolling-updates* directory to your Ansible server.
2. Move *kubernetes-cluster-rolling-updates* directory to Ansible roles folder.
3. Update the variables inside *<path-to-dir>/roles/kubernetes-cluster-rolling-updates/defaults/main.yml* accordingly.
4. Update the hosts to multi-groups specified in the Ansible inventory file inside *kubernetes-cluster-rolling-updates.yml*.
5. Execute the role: `ansible-playbook kubernetes-cluster-rolling-updates.yml`
