<h1>Monitor Cluster Components</h1>
* In a kubernetes cluster, we'll need a way to monitor resource metrics such as cpu and memory usage for pods and nodes in order to understand the health and performance of the cluster
  - A simple solution to this would be using the `metric-server` service in your cluster

<h2>Metrics-Server</h2>
* `Metric-Server` is a kubernetes cluster add-on that is used to collect resource utilization metrics from nodes and pods
  - It exposes these metrics to the `kube-apiserver` for autoscaling uses (that cannot function without these metrics):
    * `horizontal-pod-autoscaler`: scaling pods in and out (increases/decrease resource allocation to pods)
    * `vertical-pod-autoscaler`: scaling pods up and down (increases/decrease number of pods)

  - It enables `kubectl top` command to retrieve resource metrics:
    * `kubectl top nodes`: metrics for nodes
    * `kubectl top pods`: metrics for pods

<h4>Installing Metrics-Server</h4>
* Currently `metrics-server` is held in a helm chart that can be deployed into your cluster
  1. Add the repo:
    * `helm repo add metrics-server https://kubernetes-sigs.github.io/metrics-server/`
  2. Install the help chart:
    * `helm upgrade --install metrics-server metrics-server/metrics-server`
  3. Confirm its deployment:
    * `kubectl get deployment metrics-server -n kube-system`
  4. Confirm metrics consumption after some time:
    * `kubectl top node`

* NOTE: Installation will fail in on-prem home lab since k8 is running using self-signed certs
  - I added this line to the args on the deployment.yaml for metrics-server: `--kubelet-insecure-tls`
* UPDATE:
  - By default the kubelet serving certificates deployed by kubeadm is self-sgined
  - By setting `serverTLSBootstrap: true` in both the `kubelet-config` cm and the config.yaml at `/var/lib/kubelet/config.yaml` you force kubelet to obtain a cert and have it be signed by the cluster itself in the form of a csr
