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
spec:
  template:
  replicas: 3
  selector:
    matchLabels:
      type: front-end
    metadata:
      name: myapp-pod
      labels: 
        app: myapp
        type: front-end
    spec:
      containers:
      - name: nginx-container
        image: nginx
```

* NOTE: status `4/4` means 4 out of the 4 containers specified in the template, have been deployed (includes the helper containers)
* NOTE: replicas, spin up 1 application pod per replica
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
