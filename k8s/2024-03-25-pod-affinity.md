<h1>Pod Affinity</h1>
* Similarly to `nodeAffinity` in this [section](https://eoyebami.github.io/k8s/2023-12-28-node-affinity.html), pods can also have their own affinities as well
* Pod Affinity can be used to constrain which nodes you pods can be scheduled on based on labels of the pods already running on a node
  - Defining whether or not a pod should be scheduled on a node that has another pod that has a specific label

<h2>Pod AntiAffinity</h2>
* Pod AntiAffinity can be used to discourage pods from being scheduled together 
  - This could be used to implement HA or redundancy

<h2>Pod Affinity: Topology Keys</h2>
* `Topolgy Keys` is the Key within a node label
  - This is a required field for using `pod Affinity`, because it tells the scheduler how to treat nodes with those matching keys
  - Nodes with the matching `topologyKey` will be treated as the same node
  - This can be especially beneficial for nodes in CSP with keys that can define specific AZ or instance types, where pods can be placed based off affinity
* For example, if `affinity` is set and `topologyKey: kubernetes.io/hostname` pods matching the `labelSelector` will be scheduled on nodes that have the same hostname
  - Which could be multiple nodes, if they have the same hostname

<h2>Pod Affinity Types</h2>
* There are 4 types of node affinity that defines how it works:
  - `requiredDuringSchedulingIgnoredDuringExecution`: 
    * `DuringScheduling`: required the pods be scheduled on nodes matching the affinity rules; pods with matching labels will be scheduled together
    * `DuringExecution`: will ignore `nodeAffinity` rules if a change is made to the environment that affects the affinity, pods will remain executed on that node
  - `preferredDuringSchedulingIgnoredDuringExecution`:
    * `DuringScheduling`: prefer the pods be scheduled on nodes matching the affinity rules; if it can't schedule pods together then scheduler will ignore the affinity rules
    * `DuringExecution`: will ignore `nodeAffinity` rules if a change is made to the environment that affects the affinity, pods will remain executed on that node
* Ex:

```yml
spec:
  affinity:
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
```

<h2>Match and Mismatch Labels</h2>
* This feature is not enabled by default, and can be enabled via the `MatchLabelsKeysInPodAffinity` feature gate
* This feature allows you to define how pods with matching or mismathing labels that should be treated
  - In `podAntiAffinity`, pods with mismatching labels will be scheduled away from one another
  - In `podAffinity`, pods with mismatching labels will be scheduled together
  - In `podAffinity`, pods with matching labels will be scheduled together (assumption)
  - In `podAntiAffinity`, pods with matching labels will be scheduled away from one anther (assumption)

```yml
spec:
  affinity:
    podAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
      # ensure that pods associated with this tenant land on the correct node pool
      - matchLabelKeys:
          - tenant
        topologyKey: kubernetes.io/hostname
    podAntiAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
      # ensure that pods associated with this tenant can't schedule to nodes used for another tenant
      - mismatchLabelKeys:
        - tenant # whatever the value of the "tenant" label for this Pod, prevent
                 # scheduling to nodes in any pool where any Pod from a different
                 # tenant is running.
        labelSelector:
          # We have to have the labelSelector which selects only Pods with the tenant label,
          # otherwise this Pod would hate Pods from daemonsets as well, for example,
          # which aren't supposed to have the tenant label.
          # basically, your defining that this rule only applies pods that have this label, rather than for this to be a cluster wide rule
          matchExpressions:
          - key: tenant
            operator: Exists
        topologyKey: kubernetes.io/hostname
```  

