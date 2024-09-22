# etcd-backup-restore

```bash
Log in to the control plane. eg ssh controlplane
```

# For installing etcdctl if not installed

```bash
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
ETCDCTL_API=3 etcdctl snapshot restore /root/backup_etcd.db
```

# if want to restore it on specific location, use --data-dir flag

```bash
ETCDCTL_API=3 etcdctl --data-dir /root/newlocn snapshot restore /root/backup_etcd
```

# if used --data-dir flag then modify the etcd yaml and update volumes 

```bash
edit /etc/kubernetes/manifests/etcd.yaml

volumes:
  - hostPath:
      path: /root/newlocn 
      type: DirectoryOrCreate
    name: etcd-data

```


1. Installing etcdctl

To install etcdctl on your system if it's not already installed, you can use the etcd-client package:

bash

sudo apt update
sudo apt install etcd-client

This will install the etcdctl CLI tool, which is necessary for interacting with your etcd cluster.
2. Backing Up etcd Data
a. Find the Required Parameters

You need the following information to back up etcd data:

    etcd endpoint (--endpoints)
    CA certificate (--cacert)
    Server certificate (--cert)
    Server key (--key)

This information is available in your etcd static pod manifest file located at /etc/kubernetes/manifests/etcd.yaml. Use the following command to view the manifest:

bash

cat /etc/kubernetes/manifests/etcd.yaml

You can also verify the current etcd process by running:

bash

ps -aux | grep etcd

b. Back Up the etcd Data

Once you have the necessary information (endpoint and certificates), use the following command to back up your etcd data to the desired location (e.g., /root/backup_etcd.db):

bash

ETCDCTL_API=3 etcdctl \
  --endpoints=https://127.0.0.1:2379 \
  --cacert=/etc/kubernetes/pki/etcd/ca.crt \
  --cert=/etc/kubernetes/pki/etcd/server.crt \
  --key=/etc/kubernetes/pki/etcd/server.key \
  snapshot save /root/backup_etcd.db

c. Verify the Backup

You can verify the backup file and check its integrity using the following command:

bash

ETCDCTL_API=3 etcdctl --write-out=table snapshot status /root/backup_etcd.db

3. Restoring the etcd Snapshot

To restore the snapshot you took, follow these steps.
a. Basic Restore

To restore the snapshot to the default data directory:

bash

ETCDCTL_API=3 etcdctl snapshot restore /root/backup_etcd.db

b. Restore to a Specific Directory

If you want to restore the snapshot to a specific directory, use the --data-dir flag to specify the new location for the restored data:

bash

ETCDCTL_API=3 etcdctl snapshot restore /root/backup_etcd.db --data-dir=/root/newlocn

4. Updating the etcd Pod Manifest (if using a new directory)

If you restored the etcd data to a different location (e.g., /root/newlocn), you need to modify the etcd static pod manifest file to point to the new data directory.
a. Edit the etcd Pod Manifest

Open the etcd pod manifest file located at /etc/kubernetes/manifests/etcd.yaml:

bash

sudo vi /etc/kubernetes/manifests/etcd.yaml

b. Update the Volumes Section

Find the volumes section in the YAML file and update the hostPath to point to the new data directory you used for the restore (e.g., /root/newlocn):

yaml

volumes:
  - hostPath:
      path: /root/newlocn  # Path to the new data directory
      type: DirectoryOrCreate
    name: etcd-data

c. Restart the etcd Service

Kubernetes will automatically restart the etcd pod based on the updated manifest file. You can verify the etcd pod is running by using the following command:

bash

kubectl get pods -n kube-system | grep etcd

Once the pod is running again, etcd should be restored with the data from your snapshot.
