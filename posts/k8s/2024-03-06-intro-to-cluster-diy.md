<h1>Introduction to Cluster DIY</h1>
* When it comes to spinning up your own kubernetes cluster, there are a couple things you should consider
  - What is the purpose of the cluster
    * For educational purposes you can use either `minikube` or `kubeadm`, though I recommend spinning up a cluster from scratch without these tools in order to gain full understanding of how it works under the hood
    * For industry purposes you can use a variety of different resources such as `kubeadm` for On-Prem solutions and `EKS, GCP, AKS` for Cloud based solutions
  - How many nodes are necessary
  - Where do you want to host
    * Cloud vs On-Prem
  - What type of storage backend do you want to use
  - Will HA be required
    * If so, is a LB needed to sit in front of the `api-servers`
    * To avoid `quorum` issues caused by `network sigmentation` in HA for ETCD , it is best practice to choose an odd number of nodes
* There are 2 types of solutions for provisioning clusters
  - `Turnkey Solutions`: where you provision and configure the vms and use scripts to deploy the clusters, all maintained by you
    * Vagrant, OpenShift, and VMware use a turnkey solution
  - `Hosted Solutions`: where the provider will provision and maintain the vms for you, such as what a CSP would do
    * GKE, AKS, EKS use a hosted solution[I
* NOTE: for ETCD Clusters, use a minimum of 3 nodes, this could be using either a `stacked` or `external` topology
  - In the case that you are using a `stacked` topology for a HA cluster, you should use a minimum of 3 Master Nodes

<h2>Configure HA</h2>
* When it comes to configuring a HA cluster there are a couple components you should pay attention to.
  - `kube-api-server`: with more than one master node, you will have multiple `api-servers` running in your cluster
    * This can run in an active active mode, as any kube-apiserver can accept requests 
    * Most efficient way to split traffic across the two servers to fully utilize this HA, is to have a LB sit in front of the two of them to split this traffic
  - `controller-manager`: with more than one mater node, you cannot have these components responding to requests at the same time
    * This should run in an active and standby mode, almost like having a writer and reader instance in a db cluster
    * For `controller-manager` you can set a `--leader-elect true`, `--leader-elect-lease-duration 15s`, `--leader-elect-renew-deadline 10s`, `--leader-elect-retry-period 2s`
    * Whichever one of the multiple `controller-managers` locks unto the `kube-controller-manager` endpoint first, will be on active mode
    * The lease duration will determine how long this lock will last, default is 15s
    * The active process renews the lease on the lock for however long is specified in the renew deadline, default is 10s
    * The retry period specifies how long before the manager can try to lease the endpoint again, default is 2s
  - `scheduler`: follows a similar process as the `controller-manager` with the exact same flags as well

<h2>Configure HA for ETCD</h2>
* HA on ETCD is pretty similar to how HA works on both `controller-manager` and `scheduler`
  - A leader of the group of dbs in that cluster is selected to handle on writes to the dbs and this data is then cloned to the rest of the read instances
  - Any writes coming in to a read instance, will be forwarded to and handled by the elected leader in this HA cluster
    * Leader election process in ETCD works using a RAFT protocol, where each db in the cluster is given a random timer and whichever db completes their timer first will be elected as leader
    * After this point, it will periodically notify the remaining reader instance that it is still assuming the leader election
    * Should the leader fail to notify the other instances, they will go through another round of RAFT protocol to elect a new leader  
  - A write is only complete, when the leader is able to replicate the data to the majority of the reader instances
    * This is known as a `Quorum = N/2 +1`
    * So the `Quorum` contain a decimal, only the whole number will be taken
    * `Fault tolernace`: determines how many ETCD instances we can afford to lose, meaning anything outside of the `Quorum`
