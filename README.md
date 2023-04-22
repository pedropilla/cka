# CKA Annotations

## Pre start
```bash
export do='--dry-run=client -o yaml'
export now='--grace-period 0 --force'
export nk='-n kube-system'
alias kaf='k apply -f '
alias kdf='k delete -f '

printf 'set expandtab\nset tabstop=2\nset shiftwidth=2\n' >> ~/.vimrc

```

## Cluster Backup and restore
### Controlplane
```bash
export ETCDCTL_API=3
# Save backup to /var/lib/etcd-from-backup
etcdctl --endpoints 127.0.0.1:2379 \ 
  --cert=/etc/kubernetes/pki/etcd/server.crt \
  --key=/etc/kubernetes/pki/etcd/server.key \
  --cacert=/etc/kubernetes/pki/etcd/ca.crt \
  snapshot save /var/lib/etcd-from-backup
service kube-apiserver stop # If installed from packages
# Restore backup to /var/lib/etcd-from-backup
etcdctl --data-dir /var/lib/etcd-from-backup \ 
  --endpoints 127.0.0.1:2379 \
  --cert=/etc/kubernetes/pki/etcd/server.crt \
  --key=/etc/kubernetes/pki/etcd/server.key \
  --cacert=/etc/kubernetes/pki/etcd/ca.crt \
  snapshot restore /var/lib/etcd-from-backup
# If installed from packages, update etcd.service data-dir to /var/lib/etcd-from-backup with this sed command.
sed -E 's/--data-dir=.*?\ /--data-dir=\/var\/lib\/etcd-from-backup/g' /etc/systemd/system/etcd.service
# Otherwise, if kubeadm was used, this sed command will update the etcd static pod manifest and trigger the restarts.
sed 's/path: \/var\/lib\/etcd/path: \/var\/lib\/etcd-from-backup/g' /etc/kubernetes/manifests/etcd.yaml
systemctl daemon-reload # If installed from packages
service etcd restart # If installed from packages
service kube-apiserver start # If installed from packages
```

## Cluster Upgrade (1 controlplane 1 worker)
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

# UPGRADE CONTROPLANE COMPONENTS (ETCD, APISERVER, SCHEDULER, CONTROLLER, PROXY, CTL)
kubeadm upgrade apply v1.27.0

# UPGRADE KUBELET

## DEBIAN
apt-get upgrade -y kubelet=1.27.0-00
## REDHAT
yum install kubelet.x86_64-1.27.0-0

systemctl restart kubelet
```
### Workers
```bash
# UPGRADE KUBELET

## DEBIAN
apt-get upgrade -y kubelet=1.27.0-00
## REDHAT
yum install kubelet.x86_64-1.27.0-0

kubeadm upgrade node config --kubelet-version v1.27.0
systemctl restart kubelet
```

## Rollouts and rollbacks
```bash
kubectl rollout status deployment/app
kubectl rollout history deployment/app
kubectl rollout undo deployment/app
```

