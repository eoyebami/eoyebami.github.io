<h1>Cluster Maintenance</h1>
* Simply creating a cluster isn't enough, learning how to manage and maintain the components of that cluster are also equally as important
<h2>Node Maintenance</h2>
* It is possible to run into a situation where a node goes down or may need to be taken down to perform necessay OS upgrades
* Be aware that if a node is down for more than 5 min, then kubernetes will terminate all pods on that node and reschedule them in other nodes (if the pod is part of a replicaset)
* In order to safely execute a pod rescheduling on nodes you intend to bring down or have already gone down run a `kubectl drain <node-name>`
  - This will drain and reschedule whatever pods are on that node
  - The node is then marked as `Unscheduleable`
* Whenever the node comes back online you'll need to run `kubectl uncordon <node-nam>`
  - This will mark the node as `Scheduleable` again
* If you would like to mark a node as `Uncheduleable` without terminating the pods on that node run `kubectl cordon <node-name>`
<h2>Cluster Upgrade</h2>
* Semantic versioning on the core kubernetes components (`kube-api-server`, `controller-manager`, `kube-scheduler`, `kubelet`, `kube-proxy`) are grouped and packaged together
  - Meaning when a new version of one core component is released, then so are the others
  - Differences in versions can be seen on external resources like `kubectl` and `coreDNS` `etcd` which are maintained outside of the core components
* Version compatibility issues may arise
  - Since all core components commuicate with `kube-apiserver`, there should not be a core component with a higher version that the api-server
  - `Controller-manager` and `kube-scheduler` can be at one version lower than `kube-api-server`
  - `Kubelet` and `kube-proxy` can be at two versions lower than `kube-api-server`
  - `Kubectl` can be at one version higher or lower than the `kube-api-server`
* At any given moment, kubernetes only supports up to 3 minor versions behind the latest, use this for determining when to upgrade your cluster components
  - It is highly recommended to upgrade your cluster, one minor version at a time
* Node upgrades should go from Master -> Worker
  - Upgrade the Master nodes first, during which all access to the cluster will be down because the control plane components on the master will also be down
  - Workloads on the worker nodes will still function as usually until the Master nodes come back up
  - When it come time to upgrade the Worker nodes, use the strategy in the above section to minimize downtime, by draining nodes before running an upgrade on the control plane component binaries
    * Another option would be to spin up new nodes to take the place of the old nodes; where the new nodes would already have the upgrade control plane components installed
<h3>Cluster Upgrade: KubeAdm</h3>
* Use KubeAdm to upgrade your cluster
* May need to change package repo for minor versions, more info [here](https://kubernetes.io/docs/tasks/administer-cluster/kubeadm/kubeadm-upgrade/#changing-the-package-repository)
  1. `kubectl drain <node-name>`: drain master node
  2. `apt-cache madison kubeadm`: check which version of kubeadm is available for installation
  3. `sudo apt-mark unhold kubeadm && sudo apt-get update`: update system
  4. `sudo apt-get install -y kubeadm='1.28.x-*' && sudo apt-mark hold kubeadm`: update kubeadm
  5. `kubeadm upgrade plan`: displays plan for upgrade cluster
  6. `kubeadm upgrade apply 1.28.x`: upgrades cluster to specified version
  7. `sudo apt-mark unhold kubelet && sudo apt-get update`: update system
  8. `sudo apt-get install -y kubelet='1.28.x-*'`: update kubelet
  9. `sudo apt-mark hold kubelet && sudo systemctl daemon-reload && sudo systemctl restart kubelet`: restart service
  10. `kubectl uncordon <node-name>`: enable scheduling for master
  11. On any worker nodes us `kubeadm upgrade node`: since it has no controlplane components, the upgrade kubelet on that node as well
