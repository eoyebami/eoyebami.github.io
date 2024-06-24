<h1>Service Networking</h1>
 
* We've went over pod networking through the cni (more information [here](https://eoyebami.github.io/k8s/2024-02-19-pod-networking.html))
* But how does service networking for this pods occur
  - Its is understood that when a service is created, the endpoints of its governed pods become access to all components in that cluster across all nodes
    * But how exactly does this happen
* `Kubelet` is responsible for pod management, and monitors the `api-server` for any pod specific changes, which it will respond to by invoking the cni anc crt
* `kube-proxy` also runs on all worker nodes and is responsible for any service specific changes
  - For services, they are completely virtual objects with no process, interfaces, or namespaces
    * When a service is created, it receives an ip addr from a predefined range (set in the `kube-api-server` as `--service-cluster-ip-range=10.0.0.0/24`; the cidr should differ from the ones set in the ipam of your cni; more info [here](https://eoyebami.github.io/k8s/2024-02-19-cni.html))
    * Then the `kube-proxy` running on each node, gets that ip addr and creates forwarding rules on each node in that cluster
    * Forwarding all traffic to that service's ip to the backend endpoints of that service
  - `Kube-proxy` does this by using 1 of 3 options in its `--proxy-mode [userspace | iptables | ipvs]`
    * Default is set to `iptables`; when a service is created you can run the `iptables -L -t nat | grep <service>` to see the rule created by `kube-proxy`

