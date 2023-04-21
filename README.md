# CKA Annotations


## Cluster upgrade proccess
### Controlplane
```bash
kubeadm upgrade plan

# UPGRADE KUBEADM TOOL

## DEBIAN
apt-cache madison kubeadm #Show all versions of kubeadm available
apt-get upgrade -y kubeadm=1.27.0-00 #Install specific version
## REDHAT
yum --showduplicates list kubernetes-kubeadm #Show all versions of kubeadm available
yum install kubeadm.x86_64-1.27.0-0 #Install specific version

# UPGRADE CONTROPLANE COMPONENTS (APISERVER, SCHEDULER, CONTROLLER, PROXY, CTL)
kubeadm upgrade apply v.1.27.0

# UPGRADE KUBELET

## DEBIAN
apt-get upgrade -y kubelet=1.27.0-00
## REDHAT
yum install kubelet.x86_64-1.27.0-0

systemctl restart kubelet
```
### Workers
```bash
# UPGRADE KUBEADM TOOL

## DEBIAN
apt-cache madison kubeadm #Show all versions of kubeadm available
apt-get upgrade -y kubeadm=1.27.0-00 #Install specific version
## REDHAT
yum --showduplicates list kubernetes-kubeadm #Show all versions of kubeadm available
yum install kubeadm.x86_64-1.27.0-0 #Install specific version

# UPGRADE KUBELET

## DEBIAN
apt-get upgrade -y kubelet=1.27.0-00
## REDHAT
yum install kubelet.x86_64-1.27.0-0

kubeadm upgrade node config --kubelet-version v1.27.0
systemctl restart kubelet
```
