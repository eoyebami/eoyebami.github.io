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
        nodeAffinity: (labels should exist first)
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
            - matchExpressions:
              - key: kubernetes.io/hostname
                operator: In
                values:
                - controlplane
      tolerations: (taint should exist)
      - key: "app"
        operator: "Equal"
        value: "Jenkins"
        effect: "NoExecute"
      priorityClassName: high-priority
      containers:
      - name: fluentd
        image: registry.k8s.io/fluentd-elasticsearch:1.20
        imagePullPolicy: IfNotPresent
        command: ["/bin/echo"]
        args: ["Hello World"]
        env:
        - name: USER
          valueFrom: 
            configMapKeyRef:
              name: <config-map-name>
              key: <key-name>
        - name: DB_HOST
          valueFrom: (injects a key/value pair from the secret as an environment variable)
            secretKeyRef:
              name: <secret-name>
              key: <key-name>
        envFrom: (injects the entire configmap as an environment variables)
        - configMapRef:
            name: <config-map-name>
        - secretRef:
            name: <secret-map-name>
        ports:
        - containerPort: 8080
          name: elasticsearch
```
