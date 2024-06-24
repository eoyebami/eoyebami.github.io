<h1>Storage Class</h1>
 
* When working with PVs, you will need manually create the PV before users can claim them in the cluster
  - This is known as dynamic provisioning

* Storage Classes allow for dynamic provisioning, where the cluster creates the PVs on-demanded as needed by pods on the system
  - `storageClasses` dictate how storage is created, the pvc will dictate how it is accessed
  - A `storageClass` that does not support dynamic provisioning can be used to delay pvc binding until a pod requiring it is scheduled

```yml
apiVersion: storage.k8s.io/vi
kind: StorageClass
metadata:
  name: low
  annotations:
    storageclass.kubernetes.io/is-default-class: "false" # sets this as the default storageClass for all PVCs
provisioner: kubernetes.io/example # determines which volume plugin is used for provisisioning PVs
parameters: # parameters for the PV, differs for each volume plugin
  type: pd-standard
volumeBindingMode: WaitForFirstConsumer # waits for a pod using the pvc, calling this storageClass, to be created; Immediate can all be used to bind a pvc to pv immediately
reclaimPolicy: Retain # Similar values as persistentVolumeReclaimPolicy
allowVolumeExpansion: true # allows you to expand the PV by editing the PVC object, not all volume types support this
allowedTopologies: # allows restriction of PVs in supported volume plugins
- matchLabelExpressions:
  - key: topology.kubernetes.io/zone
    values:
    - us-east-2a
    - us-east-2b
    - us-east-2c
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-claim
spec:
  selector:
    matchLabels: # matchExpressions can also be used
      env: production
  storageClassName: low # if this is set to "", no SC will be associated to the PVC and all defaults will be ignored
  accessModes:
  - ReadWriteOncePod
  volumeMode: Filesystem
  resources:
    requests:
      storage: 500Mi
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-vol1
  labels: 
    name: pv-vol1 # can use labels that can be matched to selectors in pvc
spec:
  volumeMode: Filesystem
  accessModes:
  - ReadWriteOncePod
  capacity:
    storage: 1Gi
  persistentVolumeReclaimPolicy: Retain # by default this is set to Retain
  storageClassName: low # set for manually created PVs, that need to be associated to a storageClass
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

