<h1>Backup and Restore</h1>
 
* In the case when a cluster could be lost, its best to have backup options for quick application startups
<h2>Backups: Cluster Objects</h2>
 
* There are mulitple options that can be used for backing up a cluster
  - The first being to have all our manifest files save to a SCM and deploy from there
  - Another option that can be used is to simply retrieve all objects in the cluster as yaml files
    * This is especially useful in clusters that contain objects created via the imperative approach
    * `kubectl get all -all-namespaces -o yaml > all-objects.yaml`
  - One more option could be to take a backup of your etcd cluster

<h2>Restore: Cluster Objects</h2>
 
* To restore objects to a cluster, you can simply apply the files that are in your repo or run an apply on the outputted yaml file in the above example
  - `kubectl apply -f all-objects.yaml`

<h2>Backups: ETCD</h2>
 
* By default you can specify a directory where all your data in your ectd cluster will be stored
* You can also use `etcdctl` to take backups of this data periodically
  - `ETCDCTL_API=3 etcdctl snapshot save <fileName>`: saves data as a snapshot
  - `ETCDCTL_API=3 etcdctl snapshot status <fileName>`: view status of the snapshot
* NOTE: make sure you specify the `--endpoints=https://ectd:2379 --cacert=/etc/etcd/ca.crt --cert=/etc/etcd/etcd-server.crt --key=/etc/etcd/etcd-server.key` in order to authenticate with etcdctl 

<h2>Backups: ETCD</h2>
 
* By default you can specify a directory where all your data in your ectd cluster will be stored
* You can also use `etcdctl` to take backups of this data periodically
  - `ETCDCTL_API=3 etcdctl snapshot save <fileName>`: saves data as a snapshot
  - `ETCDCTL_API=3 etcdctl snapshot status <fileName>`: view status of the snapshot
* NOTE: make sure you specify the `--endpoints=https://ectd:2379 --cacert=/etc/etcd/ca.crt --cert=/etc/etcd/etcd-server.crt --key=/etc/etcd/etcd-server.key` in order to authenticate with etcdctl 

<h2>Restore: ETCD</h2>
 
* To restore a ETCD snapshot there are a couple steps to follow
  1. `service kube-apiserver stop`: disable the api-server or terminate the pod
  2. `ETCDCTL_API=3 etcdctl snapshot restore <fileName> --data-dir /var/lib/etcd-from-backup`: restore backups and configures etcd to use this new path as the data dir when mounting the dir as a volume in the pod
  3. `--data-dir=/var/lib/etcd-from-backup`: configure your etcd service to point to this new data dir by changing the volume as well
  4. `systemctl daemon-reload && service etcd restart`: reload the daemon and the service
  5. `service kube-apiserver start`: restart the api-server
* NOTE: make sure you specify the `--endpoints=https://ectd:2379 --cacert=/etc/etcd/ca.crt --cert=/etc/etcd/etcd-server.crt --key=/etc/etcd/etcd-server.key` in order to authenticate with etcdctl 
* NOTE: for kubeadm, you don't need to take down the api-server pod
* NOTE: when restoring an external etcd db, make sure the restored directory belongs to whichever user the etcd service is using to start up

