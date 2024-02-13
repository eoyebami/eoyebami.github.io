<h1>Kubernetes: Replicas</h1>
A ReplicaSet is used to manage how many instances of a pod are hosted in a cluster, for example we can have 3 replicas of an nginx pod so that if one crashes, we will still have 2 replicas remaining
  - Replicas also self-heal, so if a replica goes down, the ReplicaSet will spin up a new one
<h3>Replication Controller</h3>
* This is the legacy version of a `ReplicaSet`, below is an example of one:
- `spec`: specifications of the rs
  * `replicas`: amount of replica pods
  * `template`: template for containers in rs
    - `metadata`: metadata for the containers in pod
    - `spec`: specifications for the containers
        
```yml
apiVersion: v1
kind: ReplicationController
metadata:
  name: myapp-rc
  labels:
    app: myapp
    type: front-end
  annotations:
    buildVersion: "1.x"
spec:
  replicas: 3 # how many replicas of this template do we want
  template: # here we provide the template for the replication, in this case the pod yaml
    metadata:
      name: myapp-pod
      labels:
        app: myapp
        type: frontend
    spec:
      affinity:
        nodeAffinity: # labels should exist first
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
            - matchExpressions:
              - key: size
                operator: In
                values:
                - Small
                - Medium
      tolerations:  # taint should exist
      - key: "app"
        operator: "Equal"
        value: "jenkins"
        effect: "NoSchedule"
      priorityClassName: high-priority
      serviceAccountName: <name-of-service-account> 
      terminationGracePeriodSeconds: 30 # grace period for kubelet to wait between triggering a shut down of a failed container, default is 30s    
      containers: # we are listing the containers we want deployed in this replica controller
      - name: nginx-container
        image: nginx
        imagePullPolicy: IfNotPresent
        restartPolicy: Always # can be Never, or OnFailure
        ports:
        - containerPort: 8080
          name: http-port
        command: ["sleep"]
        args: ["1000"]
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
          allowPrivilegeEscalation: false # boolean, can this container gain more privilege than the parent process
        env:
        - name: USER
          valueFrom: # injects a key/value pair from the configmap as an environment variable 
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
        volumeMounts:
        - name: data-volume
          mountPath: /opt # mount path of volume in container
        - name: app-properties
          mountPath: /path/in/container
        - name: app-secrets
          mountPath: /path/in/container
        - name: mypd
          mountPath: /var/www/html
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
```

* All pods deployed by this replication controller will have the name of the `ReplicationController` as their pod name

<h3>ReplicaSet</h3>
* This is the latest version of a replica controller in kubernetes, below is an example of one:
* It is a process that monitors the pods that it governs
  - We use labels to allow the `ReplicaSet` to filter for pods that only match a specific label

```yml
apiversion: apps/v1
kind: ReplicaSet
metadata: 
  name: myapp-rs
  labels:
    app: myapp
    type: front-end
  annotations:
    buildVersion: "1.0"
spec:
  template: 
  replicas: 3 
  selector: # allows replicaset to identify pods that fall under it, even pods created before the replicaset
    matchLabels: # this will govern pods that have the labels defined in this dictionary
      type: front-end
    metadata:
      name: myapp-pod
      labels:
        app: myapp
        type: frontend
    spec:
      affinity:
        nodeAffinity: # labels should exist first
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
            - matchExpressions:
              - key: size
                operator: In
                values:
                - Small
                - Medium
      tolerations: # taint should exist
      - key: "app"
        operator: "Equal"
        value: "jenkins"
        effect: "NoSchedule"
      priorityClassName: high-priority
      terminationGracePeriodSeconds: 30 # grace period for kubelet to wait between triggering a shut down of a failed container, default is 30s
      serviceAccountName: <name-of-service-account> 
      containers: 
      - name: nginx-container
        image: nginx
        imagePullPolicy: IfNotPresent
        restartPolicy: Always # can be Never, or OnFailure
        commands: ["/usr/bin/echo"]
        args: ["Hello World"]
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
        env:
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
        volumeMounts:
        - name: data-volume
          mountPath: /opt # mount path of volume in container
        - name: app-properties
          mountPath: /path/in/container
        - name: app-secrets
          mountPath: /path/in/container
        - name: mypd
          mountPath: /var/www/html
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
```

* To scale a replicaset you can use:
  - `kubectl replace -f replicaset-def.yml` after modifying the file
  - `kubectl scale --replicas=6 replicaset <replica-name>`
* To replace old pods in a replicaset, you'll need to delete either the pods and let it self-heal or delete and recreate the `rs`
