<h1>Taints and Tolerations</h1>
* These are ways we can manage how pods are scheduled on nodes
  - NOTE: this does NOT define where the pod WILL be scheduled, rather which node CAN be scheduled
<h2>Taints</h2>
* Are set to restrict scheduling on a node 
  - Are defined on nodes
  - By default, no pod can tolerate any taint
    * All master nodes in a cluster are tainted to prevent nodes from being scheduled there
* Ex:
  - `kubectl taint nodes <node-name> <key=value>:<taint-effect>`: sets a taint on this node with a key/value pair
    * Ex: `kubectl taint nodes node-1 app=jenkins:NoSchedule`
  - `taint-effect`: defines what will happen to pods that do no tolerate this taint
    * `NoSchedule`: pods will not be scheduled on this node
    * `PreferNoSchedule`: pods can be scheduled on this node if there are no other options
    * `NoExecute`: new pods will not be scheduled and existing pods will be evicted (if they can't tolerate the taint)
<h2>Tolerations</h2>
* Are set to allow pods to tolerate a taint that is update a node
  - Are defined on pods
  - Pods CAN be scheduled on nodes whose taints they can tolerate
    * Just because the pod can tolerate that node, doesn't mean it will have a higher ranking on the `kube-scheduler's` priority list, so pods can be scheduled in other nodes that may rank higher
* Ex:
  - The tolerations are defined within specs section of the pods
  - By adding tolerations that match the `<key=value>/<taint-effect>` on the node, the pods can now be scheduled on that node
    * NOTE: the values need to be in double quotes `""`

  * Pod YAML:

```yml
apiVersion: vi
kind: Pod
metadata:
  name: nginx
spec:
  tolerations:
  - key: "app"
    operator: "Equal"
    value: "jenkins"
    effect: "NoSchedule"
  containers:
  - name: nginx
    image: nginx
```

  * Deployment YAML:

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels: 
    app: nginx
    tier: frontend
  annotations: 
    buildVersion: "1.0"
spec:
  replicas: 3
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
  selector:
    matchLabels:
      app: nginx
      type: frontend
  template:
    metadata:
      name: nginx
      labels: 
        app: nginx
        type: webapp
    spec:
      tolerations:
      - key: "env"
        operator: "Equal"
        value: "staging"
        effect: "NoSchedule"
      containers:
      - name: nginx-container
        image: nginx
        ports: 
        - containerPort: 8080
          name: http-port
``` 

<h2>Removing Taints</h2>
* To remove a taint from a node, simply modify the node with a `kubectl edit`
  - `kubectl edit node <node-name>`
