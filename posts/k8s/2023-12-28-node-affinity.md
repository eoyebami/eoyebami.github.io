<h1>Node Affinity</h1>
* `Node Affinity` and `Node Selectors` are very similar, in that they dictate which node a pod should be scheduled on, however `node selectors` do not allow you to perform advanced expressions.
  - In that you can't define which nodes a pod can't be scheduled on or an `OR` expression to define a variety of different nodes. 
    * This is similar to how you can use `matchExpressions` for more advanced label matching in `deployments`; using `key/operator/values` 
    * In the operator you can use `In, NotIn, Exists & DoesNotExists (no values requried), Gt, Lt`
* In the `spec` section of the definition file, you can define `affinities` or more specifically `nodeAffinity`
  - `affinity`: define pod or node affinity
    * `nodeAffinity`: set expressions for pods should be scheduled
<h2>Node Affinity Types</h2>
* There are 4 types of node affinity that defines how it works:
  - `requiredDuringSchedulingIgnoredDuringExecution`: 
    * `DuringScheduling`: required the pods be scheduled on nodes matching the affinity rules; if it can't find matching nodes, the pod will not be scheduled
    * `DuringExecution`: will ignore `nodeAffinity` rules if a change is made to the environment that affects the affinity, pods will remain executed on that node
  - `preferredDuringSchedulingIgnoredDuringExecution`:
    * `DuringScheduling`: prefer the pods be scheduled on nodes matching the affinity rules; if it can't find matching nodes, scheduler will ignore the affinity rules
    * `DuringExecution`: will ignore `nodeAffinity` rules if a change is made to the environment that affects the affinity, pods will remain executed on that node
  - `requiredDuringSchedulingRequiredDuringExecution`:
    * `DuringScheduling`: required the pods be scheduled on nodes matching the affinity rules; if it can't find matching nodes, the pod will not be scheduled
    * `DuringExecution`: will monitor changes to environment that affect `nodeAffinity` rules, if node is no longer matching, pod will be moved from that node
  - `preferredDuringSchedulingRequiredDuringExecution`:
    * `DuringScheduling`: prefer the pods be scheduled on nodes matching the affinity rules; if it can't find matching nodes, scheduler will ignore the affinity rules
    * `DuringExecution`: will monitor changes to environment that affect `nodeAffinity` rules, if node is no longer matching, pod will be moved from that node
* Ex: specifies pod has to be scheduled on any node with the label size that has any value in the list of values specified

```yml
spec: 
  containers:
  - name: nginx
    image: nginx
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: size
            operator: NotIn
            values:
            - Small
      preferredDuringScheduledIgnoredDuringExecution:
      - weight: 1  (weight can be defined for preferredDuringScheduling affinities; the higher the weight the higher the priority)
        preference:
          matchExpressions:
          - key: size
            operator: In
            values:
            - Medium
      - weight: 50
        preference:
          matchExpressions:
          - key: size
            operator: In
            values:
            - Large
``` 
