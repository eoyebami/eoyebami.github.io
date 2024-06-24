<h1>Priority Classes</h1>
 
* Pods can have priority, to indicate how important it is in respect to other pods
  - If there is no space on the cluster, the `kube-scheduler` will preempt (evict) pods with lower priority to make room for a pod with higher priority

* A `priorityClass` can be used to assign a priority to a pod
  - A `priorityClass` is a non-namespaced object which is assigned a value that determines the priority of any pod it's bound through a `priorityClassName` field
   * The higher the value the higher the priority (ranging from -2147483648 to 1000000000[1 billion])
   * Pods without a `priorityClassName` defined are automatically given a value of `0`
   * `priorityClass` only affect pods created after its own creation
   * `priorityClass` with a high enough value may violate lower priority pods that have a `PodDisruptionBudget`(limits the number of pods for a replicaset, that can be down); though it will first look for pods that do not have a PDB
   - `globalDefault`: a field defined to set that `priorityClass` as default for all pods in the cluster
   - `preemptionPolicy`: a field to define if a pod can evict lower priority pods, if set to `Never` these pods will have higher priority than low priority pods

* `priorityClass` Ex:

```yml
apiVersion: scheduling.k8s.io/vi
kind: PriorityClass
metadata:
  name: high-priority
value: 1000000000
globalDefault: false
preemptionPolicy: Never (cannot preempt other pods)
description: "for production pods"
```

* `priorityClassName` Ex:

```yml
spec:
  priorityClassName: high-priority
  containers:
  - name: nginx
    image: nginx
```    
