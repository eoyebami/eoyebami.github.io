<h1>Kubernetes Architecture</h1>
* Kubernetes architechture is comprised of multiple different components that enable it to function as a container orchestration tool. This is recorded in order to better understand how each component functions.
* We can break up Kubernetes into 2 primary nodes where all other components will fall under.
  * Master Node
    - ETCD
    - Kube-ApiServer
    - Kube Controller Manager
    - Kube Scheduler
  * Worker Node
    - Kubelet
    - Kube Proxy
    - Container Runtime Engine
<h2>ETCD</h2>
<h4>ETCD: Basics</h4>
* ETCD is a distributed reliable key/value store db. Which stores data as pages and each individual (or datagroup) gets its own file, so changes to one individual file does not effect the others (as would happen in a RDB)
* This can be installed as a service, and can be interacted with using a client (etcdctl)
  - You can use it to interact with the db
    * `etcdctl set key1 value1` or `etcd get key1`
  - ETCD and the API Version is works with can be different ( v2.* and v3.* ) which results in different etcd cmds being run
    * `etcdctl --version` : will list the versions of both
    * `etcdctl` : will show a list of commands that can be run
    * `export ETCDCTL_API=n` : can set which version of the api you want `etcdctl` to interact with
  - Default Port ETCD listens onL `2379`
<h4>ETCD: Kubernetes</h4>
* ETCD functions in k8 by storing information about all k8 objects and resources. All information you see when you run the `kubectl get` command, is from the `etcd server`.
  - Any updates you make to cluster, also gets updated on this server
* When you configure a cluster from scratch, etcd will need to be installed manually and parameters set in the service file to ensure its functionality
  - `--advertise-client-urls https://${INTERNAL_IP}:2379`: the address where etcd listens (this is the url that should be confirmed in the `kube-apiserver` when it tries to reach the `etcd server`
  - `--initial-cluster controller-0=https://${CONTROLLER0_IP}:2380,controller-1=https://CONTROLLER1_IP}:2380` : where you'll expose the other etcd servers in the case that your cluster has multiple masters
* When you configure a cluster using `kubeadm`, etcd is deployed as a pod in the `kube-system` namespace
  - you'd run the `etcdctl` commands on the server, through `kubectl exec` on the pod
<h2>Kube ApiServer</h2>
* The Kube ApiServer is the primary management component of kubernetes. When you run a `kubectl` command, a `kube-api utility` is reaching to the `kube-apiserver`.
  * It is also the only kubernetes component that interacts directly with the `etcd server`
  - The `kube-apiserver` first authenticates the request (you'll see an `Unauthorized` error is this fails)
  - Then the `kube-apiserver` then retrieves the data from the `etcd server` then responds bak with the requested information
  - Its possible to make calls to the apiserver by using the api: `curl -X POST /api/v1/namespaces/default/pods ...`
    1. The `api utility` makes a call to the `apiServer`
    2. `Apiserver` authenticates the user and validates the request
    3. It retrieves the data and updates the `etcd server`
    4. `Apiserver` updates the user that the pod has been created
    5. `Kube-Scheduler` continously monitors the `apiserver` and realizes there is a new pod with no node assigned
    6. `Kube-Scheduler` identifies a node to place the new pod and notifies the `apiserver`
    7. `Apiserver` updates this information in the `etcd server` and then places that information to the `kubelet` of the corresponding node
    8. `Kubelet` creates the pod on the node and instructions the `container runtime engine` to deploy the image into a container in the pod
    9. `Kubelet` passes the status of the pod back to the `apiserver` which then updates the information into the `etcd server`
* The `Kube-apiserver` can also be installed manually on a node and configured, with certs, to communicate with the `kubelet` on the worker nodes and the `etcd` on the master
* `Kubeadm` also deploys this service as a pod on the cluster
<h2>Kube Controller Manager</h2>
* A controller is a process that continously monitors the state of components on the cluster and works to bring the cluster up to the desired state
  - There are various types of controllers in a cluster (you can image there is one for each k8 resource, but I'll record a few below.
    * They are all packaged into a process known as the `kube-controller-manager`
* This manager can be installed manually and the configs for each type of controller can also be defined in its service file
  - by default, all controllers are enabled, but you can select a few to enable
* `Kubeadm` also deploys this service as a pod on the cluster
<h4>Kube Controller Manager: Node Controller</h4>
* The `node controller` monitors the status of the node and takes necessary action to keep the application running through the `kube-apiserver`
  - It checks the status of the node every 5s (by default)
  - If a node goes down, it will wait 40s before marking it as unreachable (by default)
  - After a node is marked as unreachable, it keeps it 5m to come back up before removing pods assigned to that node and provisioning them on healthy node
<h4>Kube Controller Manager: Replication Controller</h4>
* The `replication controller` monitors the status of `replicasets` and ensures the desire number of pods are available, through the `kube-apiserver`
<h2>Kube Scheduler</h2>
* The `kube-scheduler` is responsible for deciding which pod goes on which node, it does not actually place the pods in the node.
  1. The scheduler first goes through a node filtration, where it filters out nodes that do not have the resource requirements to host the pods
  2. Then the scheduler ranks the remaining nodes by using a priority function, from 0-10, to determine things like how much resources will remain on the node after the pod is scheduled
    - This can be customized and you can write your own scheduler as well
    - Resource requirements and limits for pods, taints and tolerations, and node and pod selectors/affinity can affect this ranking system
* The `Kube-scheduler` can also be installed manually on a node and configured, with certs, to communicate with the `kubelet` on the worker nodes and the `etcd` on the master
* `Kubeadm` also deploys this service as a pod on the cluster
<h2>Kubelet</h2>
* `Kubelet` performs multiple tasks at the node level:
  - Registers nodes to the cluster
  - Load and unload pods in the node as instructed by the `kube-scheduler`
  - They send status reports of the node back to the `apiserver` which updates the `etcd server`
* Regardless if you are installing K8 manually or through kubeadm, `kubelet` must always be manually installed on the worker nodes
<h2>Kube Proxy</h2>
* `Kube-proxy` is a process that runs on each node, and its job is to look for new `services` and to creates the appropriate rules on each node to forward traffic to the backend pods
  - It creates an ip tables rule to forwards traffic heading to the ip of the `service` to the ip of the actual pod
* The `Kube-proxy` can also be installed manually on a node
* `Kubeadm` also deploys this service as a pod on the cluster
