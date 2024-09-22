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

# While restoring you will encounter this Error:  expected sha256 [31 177 140 0 216 81 177 0 87 83 236 160 39 211 136 74 253 154 69 183 239 52 21 140 204 125 229 88 184 107 27 188], got [31 45 65 183 167 83 150 10 78 77 66 48 18 11 145 41 22 100 91 236 46 237 40 147 153 217 69 210 162 171 92 227] to solve them use --skip-hash-check=true

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
kubectl describe pod etcd-controlplane -n kube-system
```

