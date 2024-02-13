<h1>Persistent Volumes</h1>
* Persistent Volumes are a way for users within a cluster to manage volumes more centrally
  - Rather than a user having to generate a volume in the host machine or with whichever 3rd party drive I'm using, I can just pull from a pool of storage already configured for the cluster
* The admin would configure a pool of cluster wide storage that users could then access by using `persistentVolumeClaims`
<h2>PV: Access Modes</h2>
* There are four types of access modes a PV can have
  - `ReadWriteOnce (RWO)`: the volume can be mounted as rx by a singe node, multiple pods in the same node can access the volume
  - `ReadOnlyMany (ROX)`: the volume can be mounted as ro by many nodes
  - `ReadWriteMany (RWX)`: the volume can be mounted as read-write by many nodes
  - `ReadWriteOncePod (RWOP)`: the volume can be mounted as rx by a single pod
* Specific plugins can handle specific access modes, more info [here](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)
<h2>PV: Volume Modes</h2>
* Volume modes dictate the type of PV
  - `Filesytem`: a filesystem PV backed by a block device
  - `Block`: a raw block device
    * Pod must know how to handle the block device, more info [here](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#raw-block-volume-support)
<h2>PV: Reclaim Policy</h2>
* Defines what should happen to the volume when its released from a pvc
  - `Retain`: the data is preserved
  - `Delete`: volume is deleted
  - `Recycle`: data is scrubbed from volume
    * Only nfs and hostpath support this

```yml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-vol1
  labels: 
    name: pv-vol1 # can use labels that can be matched to selectors in pvc
spec:
  volumeMode: Filesystem
  accessModes:
  - ReadWriteOnce
  capacity:
    storage: 1Gi
  persistentVolumeReclaimPolicy: Retain # by default this is set to Retain
  hostPath:
    path: /tmp/data
  nodeAffinity: # can be used to constrain pods which use this PV to specific nodes
    required:
      nodeSelectorTerms:
      - matchExpressions:
        - key: kubernetes.io/hostname
          operator: In
          values:
          - node1
          - node2
```

<h2>Persistent Volume Claim</h2>
* Persistent Volume Claims are how users claim a `PersistentVolume`, when a pvc is created kubernetes tries to find a pv that matches the requests and binds them together
  - A smaller claim can be bound to a bigger volume is there are no other better options
    * Excess space in a pv cannot be used by another pvc

```yml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-claim
spec:
  selector:
    matchLabels: # matchExpressions can also be used
      env: production
  accessModes:
  - ReadWriteOncePod
  volumeMode: Filesystem
  resources:
    requests:
      storage: 500Mi
```

<h2>Manifest File</h2>
* Once the pvc has been made, you can specify it in the `persistentVolumeClaim` portion in the volumes section

```yml
spec:
  containers:
  - name: myfrontend
    image: nginx
    volumeMounts:
    - mountPath: "/var/www/html"
      name: mypd
  volumes:
  - name: mypd
    persistentVolumeClaim:
      claimName: pvc-claim
```

