<h1>Static Pods</h1>
* Static pods run on isolated worker nodes (nodes with no other kubernetes components besides the `kubelet` and `CRT` running on the node)
  - With no `apiserver` or other kubernetes components, the `kubelet` can only manage pods
  - `kubelet` recieves these instructions by looking in a `pod-manifest-path` set during the `kubelet` configuration
    * `--pod-manifest-path=/path/to/manifest` 
    * `--config=kubeconfig.yaml (which will contain: `staticPodPath: /path/to/manifest` to locate the pod-definitions) -> Used by `KubeAdm`
    * `ps -ef | grep kubelet` : to find the service in a preexisting cluster
  * `kubelet` will periodically check this path for new manifest or changed manifests and will destroy and create them based on the changes in this directory (removed manifests, result in corresponding pods being immediately terminated)
    - pods created by `kubelet` will be suffixed with the hostname of the node that the `kubelet` is hosted on
* NOTE: since it has no kubernetes functionality, you will need to use the `CRT` to interact with the pocs: `docker ps`
  - However, if the worker node is apart of a cluster, the `api-server` will still be aware of the `static pods` as it create a `ro mirror` of the pod in the `api-server`
    * You will be able to see details of the `static-pods` with the `kubectl` command but you won't be able to interact with it
* The purpose of these pods, would be to aid in your own cluster creation by using them to host the other kubernetes control plane components `(apiserver, kube-controller, etcd, kube-scheduler)`
  - By first installing `kubelet`; you can have it manage the other components in the `pod-manifest-path`
    * Should the services crash, `kubelet` will spin them back up to ensure synergy between the pods running and the manifest files
    * This is what `KubeAdm` does, and why you see the other control plane components running as pods in the `kube-system` namespace
