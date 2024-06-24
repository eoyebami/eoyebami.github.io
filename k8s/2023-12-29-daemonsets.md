<h1>DaemonSet</h1>
 
* `DaemonSets` are similar to `replicasets`, the only difference is they work to deploy one pod on each node within the cluster
  - They are used mainly for logging and monitoring
    * Like with `Datadog`, you'd like to have one agent running in each node to collect metrics, sort of like a `daemon` service running in the `bg` in a node, these pods will act as such
  - If a new node is added to the cluster, then the `daemonset` will deploy a new replica into that node
  - if a node is removed from a cluster, then the `daemonset` will remove the replica from that node
  - `Taints, tolerations, and nodeAffinity` can be used to define which nodes these pods will be scheduled on in the cluster

* Ex:
```yml
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
        nodeAffinity: # labels should exist first
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
            - matchExpressions:
              - key: kubernetes.io/hostname
                operator: In
                values:
                - controlplane
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
      tolerations: # taint should exist
      - key: "app"
        operator: "Equal"
        value: "Jenkins"
        effect: "NoExecute"
      priorityClassName: high-priority
      serviceAccountName: <name-of-service-account>
      terminationGracePeriodSeconds: 30 # grace period for kubelet to wait between triggering a shut down of a failed container, default is 30s
      containers:
      - name: fluentd
        image: registry.k8s.io/fluentd-elasticsearch:1.20
        imagePullPolicy: IfNotPresent
        restartPolicy: Always # can be Never, or OnFailure
        command: ["/bin/echo"]
        args: ["Hello World"]
        env:
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
        ports:
        - containerPort: 8080
          name: elasticsearch
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
