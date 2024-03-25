<h1>Kubernetes: Deployments</h1>
Kubernetes Deployments allow for rolling updates and rollbacks in case of application failure with a new release. This is beneficial, especially for production environments.
- If K8 had a hierarchy it would go, `Container -> Pods -> ReplicaSets -> Deployments`
  * Which in Deployments you are able to define which containers are in a pod and how many replicas of the pod
<h2>YAML: Deployments</h2>
* The formating of a `Deployment` is almost completely identical to a `ReplicaSet`
  - When you launch a `Deployment` it creates a `ReplicaSet`, `Pods`, and `Containers`

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
   name: myapp-deployment
   labels:
     apps: myapp
     type: front-end
   annotations:
     buildVersion: "1.x" # records details for informative purposes, must be in quotes
spec:
  replicas: 3
  selector: # matches pods created by a Deployment, this matchLabels must be the same as the labels included in the template for proper matching, labels here will be selected by the Service
    matchLabels: # can be either matchLabels or matchExpressions
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
      affinity:
        nodeAffinity: # labels must exist first
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
            - matchExpressions:
              - key: env
                operator: In
                values:
                - Production
                - Pre-Production
          preferredDuringSchedulingIgnoredDUringExecution:
          - weight: 50
            preference:
              matchExpressions:
              - key: size
                operator: In
                values:
                - Large
          - weight: 1
            preference:
              matchExpressions:
              - key: size
                operator: In
                values:
                - Medium
        podAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
          - labelSelector: # requires that pods with these labels be scheduled together
              matchExpressions:
              - key: security
                operator: In
                values:
                - S1
            topologyKey: kubernetes.io/hostname # nodes with this key will be treated as the same
        podAntiAffinity:
          preferredDuringSchedulingIgnoredDuringExecution:
          - weight: 100 # scheduler will prefer to schedule pods, with matching labels, away from one another
            podAffinityTerm:
              labelSelector:
                matchExpressions:
                - key: security
                  operator: In
                  values:
                  - S2
              topologyKey: kubernetes.io/hostname # nodes with this key will be treated the same
      tolerations: # taint must be set first
      - key: "size"
        operator: "Equal"
        value: "Large"
        effect: "NoSchedule"
      priorityClassName: high-priority
      serviceAccountName: <name-of-service-account>
      terminationGracePeriodSeconds: 30 # grace period for kubelet to wait between triggering a shut down of a failed container, default is 30s
      containers:
      - name: nginx-container
        image: nginx
        imagePullPolicy: IfNotPresent
        restartPolicy: Always # can be Never, or OnFailure
        env: # you can add environment variables directly through the deployment.yaml
        startupProbe:
          initialDelaySeconds: 3 # seconds k8 should wait before performing first liveness probe
          periodSeconds: 3 # time between liveness probes
          failureThreshold: 12 # how many times the probe can fail becuase k8 considers the overall check a fail and triggers a restart
          timeoutSeconds: 10 # number of seconds before probe times outs, default is 1s
          httpGet: # specifies a HTTP-based liveness check at a path and port
            path: /healthz
            port: 80
        livenessProbe:
          initialDelaySeconds: 3 # seconds k8 should wait before performing first liveness probe
          periodSeconds: 3 # time between liveness probes
          failureThreshold: 12 # how many times the probe can fail becuase k8 considers the overall check a fail and triggers a restart
          timeoutSeconds: 10 # number of seconds before probe times outs, default is 1s
          httpGet: # specifies a HTTP-based liveness check at a path and port
            path: /healthz
            port: 80
        readinessProbe:
          initialDelaySeconds: 3 # seconds k8 should wait before performing first readiness probe
          timeoutSeconds: 10 # number of seconds before probe times outs, default is 1s
          periodSeconds: 3 # time between readiness probes
          failureThreshold: 12 # how many times the probe can fail becuase k8 considers the overall check a fail and triggers a restart
          httpGet: # specifies a HTTP-based readiness check at a path and port
            path: /index.html
            port: 80
        securityContext:
          runAsUser: 1001
          runAsNonRoot: true # boolean param
          allowPrivilegeEscalation: false # boolean, can this container gain more privilege than the parent proces
        - name: USER
          value: "xxxxx"
        - name: PASSWORD
          value: "xxxxx"
        - name: MESSAGE
          value: "Hello World"
        - name: USER
          valueFrom:
            configMapKeyRef:
              name: <config-map-name>
              key: <key-name>
        - name: DB_HOST
          valueFrom: # injects a key/value pair from the secret as an environment variable
            secretKeyRef:
              name: <secret-name>
              key: <key-name>
        envFrom: # injects the entire configmap as an environment variables
        - configMapRef:
            name: <config-map-name>
        - secretRef:
            name: <secret-map-name>
        command: ["/bin/sh"] (defines command to be run when the container spins up [in this case we ran a shell])
        args: ["-c", "while true; do echo $(MESSAGE); sleep 10; done"] # defines arguments for that command [we can provide flags as args, multiple can be set, env variables set in the pod can also be used]
        ports:
        - containerPort: 80
          name: nginx-http-port
        - containerPort: 443
          name: nginx-https-port
        volumeMounts:
        - name: data-volume
          mountPath: /opt # mount path of volume in container
        - name: app-properties
          mountPath: /path/in/container
        - name: app-secrets
          mountPath: /path/in/container
          readOnly: true
        - name: mypd
          mountPath: /var/www/html
        - name: podinfo
          mountPath: /etc/podinfo
      volumes:
      - name: mypd
        persistentVolumeClaim:
          claimName: myclaim
      - name: data-volume
        hostPath:
          path: /data
          type: Directory # potential values are; DirectoryOrCreate, Directory, FileorCreate, File, Socket, CharDevice, BlockDevice
      - name: app-properties
        configMap: # mounts the configmap as a file in the pod
          name: <config-map-name>
      - name: app-secrets
        secret: # mounts each secret as a seperate file in the pod; 3 data attributes = 3 files
          secretName: <secret-name>
      - name: empty-volume
        emptyDir:
          sizeLimit: 500Mi # can specifiy a limit to the size of this empty directory
      - name: podinfo
        downwardAPI:
          items:
          - path: "labels"
            fieldRef:
              fieldPath: metadata.labels
          - path: "annotations"
            fieldRef:
              fieldPath: metadata.annotations
```

* NOTE: status `4/4` means 4 out of the 4 containers specified in the template, have been deployed (includes the helper containers)
* NOTE: replicas, spin up 1 application pod per replica
<h2>Deployments: Selectors for Services</h2>
* You can use label to match pods created by deployments, through selectors `matchLabels` or `matchExpressions`. This value will be given to a `Service` to ensure it matches to pods managed by that specific `Deployment`.
  - The label you define here, is what you provide the `Service` to ensure traffic is directed to pods managed by that `Deployment`.
  - Common practice is to make the `Deployment Selectors` and the `Pod Selectors` match.
<h4>matchLabels</h4>
* You can match Labels from the selector you'd like exposed, through key/value pairs
```yml
selector:
  matchLabels:
    app: voting-app
```

<h4>matchExpressions</h4>
* You can use match labels from the selector you'd like exposed, through key/operator/values which give you more options
  - In `operator` you can use `In, NotIn, Exists(if the key exists), DoesNotExist(if key does not exist), Gt, Lt`
  - In `values` you can define an array that will allow an `OR` requirement rather than an `AND` for labels

   ```yml
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
```yml
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
```yml
spec:
  replicas: 10
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 25% (integers/percentages accepted)
      maxUnavailable: 25% (integers/percentages accepted)
```
