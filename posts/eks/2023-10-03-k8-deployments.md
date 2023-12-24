<h1>Kubernetes: Deployments</h1>
Kubernetes Deployments allow for rolling updates and rollbacks in case of application failure with a new release. This is beneficial, especially for production environments.
- If K8 had a hierarchy it would go, `Container -> Pods -> ReplicaSets -> Deployments`
  * Which in Deployments you are able to define which containers are in a pod and how many replicas of the pod
<h2>YAML: Deployments</h2>
* The formating of a `Deployment` is almost completely identical to a `ReplicaSet`
  - When you launch a `Deployment` it creates a `ReplicaSet`, `Pods`, and `Containers`

```
apiVersion: apps/v1
kind: Deployment
metadata:
   name: myapp-deployment
   labels:
     apps: myapp
     type: front-end
   annotations:
     buildVersion: "1.x" (records details for informative purposes, must be in quotes)
spec:
  replicas: 3
  selector: (matches pods created by a Deployment, this matchLabels must be the same as the labels included in the template for proper matching, labels here will be selected by the Service)
    matchLabels: (can be either matchLabels or matchExpressions)
      type: front-end
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
  template:
    metadata:
      name: myapp-pod
      labels: 
        app: myapp
        type: front-end
    spec:
      containers:
      - name: nginx-container
        image: nginx
        ports:
        - containerPort: 80
          name: nginx-http-port
        - containerPort: 443
          name: nginx-https-port
        env: (you can add environment variables directly through the deployment.yaml)
        - name: USER
          value: "xxxxx"
        - name: PASSWORD
          value: "xxxxx"
```

* NOTE: status `4/4` means 4 out of the 4 containers specified in the template, have been deployed (includes the helper containers)
* NOTE: replicas, spin up 1 application pod per replica
<h2>Deployments: Selectors for Services</h2>
* You can use label to match pods created by deployments, through selectors `matchLabels` or `matchExpressions`. This value will be given to a `Service` to ensure it matches to pods managed by that specific `Deployment`.
  - The label you define here, is what you provide the `Service` to ensure traffic is directed to pods managed by that `Deployment`.
  - Common practice is to make the `Deployment Selectors` and the `Pod Selectors` match.
<h4>matchLabels</h4>
* You can match Labels from the selector you'd like exposed, through key/value pairs
```
selector:
  matchLabels:
    app: voting-app
```

<h4>matchExpressions</h4>
* You can use match labels from the selector you'd like exposed, through key/operator/values which give you more options
  - In `operator` you can use `In, NotIn, Exists(if the key exists), DoesNotExist(if key does not exist), Gt, Lt`
  - In `values` you can define an array that will allow an `OR` requirement rather than an `AND` for labels

```
selector:
  matchExpressions:
  - {key: app, operator: In, values: [production, staging]}
```

<h2>Deployments: Update and Rollback</h2>
* With Kubernetes Deployments, you are able to check past deployment histories and status of current deployments:
  - Ex:
    * `kubectl rollout status deploy <deployment_name>`
    * `kubectl rollout history deploy <deployment_name>`
<h4>Update</h4>
* After updating the user-definitions yaml deployment file, you can run a `kubectl apply -f <file_name>` to do a rolling update (blue-green deployment)
  - you can also do a `kubectl rollout restart deployment <deploy_name>`
<h4>Rollback</h4>
* After rolling out an update, if you find that you need to rollback to the previous version, you can run a:
  - `kubectl rollout undo deployment <deploy_name>`
* When you rollout a deployment, if you give it a `--record` flag, kubernetes will record why the change was made, when you check the history.
  - This will usually show which command was used to initiate the change
<h2>Deployments: Strategies</h2>
* With Kubernetes Deployments, you can specifiy the type of strategy an update and a rollout will do
<h4>Recreate</h4>
* This strategy will terminate all pods and replace them with the new version. Beneficial when old and new versions of the application cannot run at the same time. 
  - Will cause downtime
* Ex:
```
spec:
  replicas: 10
  strategy:
    type: Recreate
```

<h4>RollingUpdate</h4>
* This is the default strategy for kubernetes, it runs new and old versions of application together and spins up new ones and terminates the one versions of the application as the new version becomes available
  - It uses `readiness probes`, which monitor when the application becomes available, which in turn disables traffic to the old version of the application and terminates
  - If there is a problem, the rollout can be stopped and rollbacked to previous version
  - It can refined with the below parameters:
    * `maxSurge`: specifies the maxium number of pods the deployment can create at one time
    * `maxUnavailable`: specifies the maximum number of pods that are allowed to be unavailable during the rollout
* Ex: 
```
spec:
  replicas: 10
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 25% (integers/percentages accepted)
      maxUnavailable: 25% (integers/percentages accepted)
```
