# etcd-backup-restore

```bash
Log in to the control plane. eg ssh controlplane
```

# For installing etcdctl if not installed

```bash
sudo apt update
sudo apt install etcd-client
```

# For backing up , etcdctl need these :

```bash
    etcd endpoint (–endpoints)
    ca certificate (–cacert)
    server certificate (–cert)
    server key (–key)
```

# etcd static pod manifest file

```bash
cat /etc/kubernetes/manifests/etcd.yaml
```

# optional way to check the above values 

```bash
ps -aux | grep etcd
```

# cli utility to backup and save in desired location

```bash
ETCDCTL_API=3 etcdctl \
  --endpoints=https://127.0.0.1:2379 \
  --cacert=/etc/kubernetes/pki/etcd/ca.crt \
  --cert=/etc/kubernetes/pki/etcd/server.crt \
  --key=/etc/kubernetes/pki/etcd/server.key \
  snapshot save /root/backup_etcd.db
```

# to verify correct backing up

```bash
ETCDCTL_API=3 etcdctl --write-out=table snapshot status /root/backup_etcd.db
```

### Now to restore the snapshot you took 

```bash
ETCDCTL_API=3 etcdctl snapshot restore /root/backup_etcd.db --skip-hash-check=true
```

# if want to restore it on specific location, use --data-dir flag

```bash
ETCDCTL_API=3 etcdctl snapshot restore /root/backup_etcd.db --data-dir=/root/newlocn --skip-hash-check=true
```

# if used --data-dir flag then modify the etcd yaml and update volumes 

```bash
sudo vi /etc/kubernetes/manifests/etcd.yaml


volumes:
  - hostPath:
      path: /root/newlocn  # Path to the new data directory
      type: DirectoryOrCreate
    name: etcd-data
```

# Restart etcd

```bash
kubectl get pods -n kube-system | grep etcd
kubectl get po -n kube-system
kubectl describe pod etcd-master-node -n kube-system
```

