<h1>DaemonSet</h1>
* `DaemonSets` are similar to `replicasets`, the only difference is they work to deploy one pod on each node within the cluster
  - They are used mainly for logging and monitoring
    * Like with `Datadog`, you'd like to have one agent running in each node to collect metrics, sort of like a `daemon` service running in the `bg` in a node, these pods will act as such
  - If a new node is added to the cluster, then the `daemonset` will deploy a new replica into that node
  - if a node is removed from a cluster, then the `daemonset` will remove the replica from that node
  - `Taints, tolerations, and nodeAffinity` can be used to define which nodes these pods will be scheduled on in the cluster
* Ex:
```
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: elasticsearch
  namespace: kube-system
  labels:
    app: fluentd
    tier: monitoring
  annotations:
    buildVersion: "1.20"
spec:
  selector:
    matchLabels:
      app: fluentd
      tier: monitoring
  template:
    metadata:
      labels:
        app: fluentd
        tier: monitoring
    spec:
      affinity:
        nodeAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
            - matchExpressions:
              - key: kubernetes.io/hostname
                operator: In
                values:
                - controlplane
      containers:
      - name: fluentd
        image: registry.k8s.io/fluentd-elasticsearch:1.20
        command: ["/bin/echo"]
        args: ["Hello World"]
        ports:
        - containerPort: 8080
          name: elasticsearch
```
