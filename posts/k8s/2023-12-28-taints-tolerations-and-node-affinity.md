<h1>Taints, Tolerations, and Node affinity</h1>
* There may be cases in which you will need to use a combination of these 3 scheduling methods:
  - You have pods that need to be scheduled on specific nodes, while you also have pods that you don't want scheduled on those same nodes
* Solution:
  1. Taint the node
    - by default no pods can tolerate the taint
    - `kubectl taint node node-01 color=blue:NoExecute`: evicts all pods that don't tolerate the taint
  2. Add labels to the node
    - to allow node selectors to be defined for the deployment
    - `kubectl label node node-01 color=blue`
  3. Set a toleration for the pods you want to allow scheduling, on those nodes, for
  4. Add node affinity rules for the pods to ensure they are scheduled on those nodes
     
```yml
apiVersion: v1
kind: Deployment
metadata:
  name: blue
  labels:
    app: web-server
    tier: front-end
  annotations:
    buildVersion: "v1"
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
      tier: front-end
  template:
    metadata:
      name: nginx
      labels:
        app:nginx
        tier: front-end
    spec:
      tolerations:
      - key: "color" 
        operator: "Equal" 
        value: "blue"
        effect: "NoExecute"
      affinity:
        nodeAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
            - matchExpressions:
              - key: color
                operator: In
                values:
                - blue
      containers:
      - name: nginx
        image: nginx
        ports:
        - containerPort: 8080
           name: http-port
```
