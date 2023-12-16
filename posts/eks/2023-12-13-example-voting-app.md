<h1>Example Voting App</h1>
- Steps:
  1. Deployments for Pods
    - voting-app
    - redis
    - woker
    - postgresql
    - result-app
  2. Create Services
    - front-end: voting-app
    - front-end: result-app
    - backend: redis
    - backend: postgresql
<h2>Voting App Deployment & Service:</h2>
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: voting-app
  labels:
    app: voting-app
    type: front-end
spec:
  selector:
    matchLabels:
      app: voting-app
      type: front-end
  replicas: 3
  strategy: 
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
  template:
    metadata:
      labels:
        app: voting-app
        type: front-end
    spec:
      containers:
      - name: voting-app
        image: kodekloud/examplevotingapp_vote:v1
        ports:
        - containerPort: 80
          name: voting-app-http
---
apiVersion: v1
kind: Service
metadata:
  name: voting-app-svc
  labels:
    app: voting-app-svc
    type: front-end
spec:
  selector:
    app: voting-app
    type: front-end
  type: LoadBalancer
  ports:
  - name: voting-app-svc-http
    protocol: TCP
    targetPort: voting-app-http
    port: 80
```

<h2>Result App Deployment & Service:</h2>
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: result-app
  labels:
    app: result-app
    type: front-end
spec:
  replicas: 3
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge 25%
      maxUnavailable: 25%
  selector:
    matchLabels:
      app: result-app
      type: front-end
  spec:
    template:
      metadata:
        app: result-app
        type: front-end
      spec:
        containers:
        - name: result-app
          image: kodekloud/examplevotingapp_result:v1
          ports: 
          - containerPort: 80
            name: result-app-http
---
apiVersion: apps/v1
kind: Service
metadata:
  name: result-app-svc
  labels:
    app: result-app-svc
    type: front-end
spec:
  selector:
    app: result-app
    type: front-end
  type: LoadBalancer
  ports:
  - name: result-app-http-svc
    protocol: TCP
    targetPort: result-app-http
    port: 80
```

<h2>Redis App Deployment & Service:</h2>
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis
  labels:
    app: redis
    type: back-end
spec:
  replicas: 3
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
  selector:
    matchLabels:
      app: redis
      type: back-end
  template:
    metadata:
      app: redis
      type: back-end
    spec:
      containers:
      - name: redis
        image: redis
        ports:
         - containerPort: 6379
           name: redis-db-port
---
apiVersion: v1
kind: Service
metadata:
  name: redis
  labels:
    app: redis-svc
    type: back-end
spec:
  selector:
    app: redis
    backend: back-end
  type: ClusterIP
  ports:
  - name: redis-db-port-svc
    protocol: TCP
    targetPort: 6379
    port: 6379env:
        - name: POSTGRES_USER
          value: "xxxxx"
        - name: POSTGRES_PASSWORD
          value: "xxxxx"
```

<h2>PostgreSql App Deployment & Service:</h2>
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: db
  labels: 
    app: db
    type: back-end
spec:
  replicas: 3
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
  selector:
    matchLabels:
      app: db
      type: back-end
  template:
    metadata:
      labels:
        app: db
        type: back-end
    spec: 
      containers:
      - name: db
        image: postgresql
        ports:
        - containerPort: 5432
          name: postgresql-db-port
        env:
        - name: POSTGRES_USER
          value: "xxxxx"
        - name: POSTGRES_PASSWORD
          value: "xxxxx"
---
apiVersion: v1
kind: Service
metadata:
  name: db
  labels:
    app: db-svc
    type: back-end
spec:
  selector: 
    app: db
    type: back-end
  type: ClusterIP
  ports:
  - name: postgresql-db-port-svc
    protocol: TCP
    targetPort: postgresql-db-port
    port: 5432
```

<h2>Worker App Deployment & Service</h2>
* To note, in order to match a Deployment to 2 separate Services, give it a label that both Services will have
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: worker-app
  labels:
    app: worker-app
    type: back-end
spec:
  replicas: 3
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
  selector:
    matchLabels:
      app: worker-app
      type: back-end
  template:
    metadata:
      app: worker-app
      type: backend
    spec:
      containers:
      - name: worker-app
        image: kodekloud/examplevotingapp_worker:v1
```
