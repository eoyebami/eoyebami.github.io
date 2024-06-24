<h1>Resource Allocation</h1>
 
* You can determine how much resources to allocate to a container using placing a `request` an `limit` value for each resource

<h2>Requests</h2>
 
* Defines the minimum amount of cpu or memory requested by a container
  - Ideally you want to define `requests` for each container without `limits`, so they are guaranteed what they need and can consume more without disrupting the minimum required for the other pods in the system (though this may vary depending on the env)
  - The `kube-scheduler` will look at this value to determine which node to place the pod which hosts this container, that will satisfy its requirements

* `resource`: define `requests` anf `limits` for the resources the container needs
  - `request`: minimum needed
    * `cpu`: expresses minimum allocation as `1 AWS vCPU, 1 GCP Core, 1 Azure Core, 1 Hyperthread` in `Gi`, the lowest you can do is `0.1` (Gi [does not need the unit]) or you can use `m`, the lowest being `1m` (needs the unit)
    * `memory`: expresses minimum allocation  as either `G, M, K, Gi, Mi, Ki` (the `i` suffixes denoting a slighty higher size than their counterparts)

<h2>Limits</h2>
 
* Defines the maximum amount of cpu and memory a container can use
  - if a container tries to exceed the limits set on the cpu, `cpu throttling` will occur to ensure that the container cannot
  - if a container tries to exceed the limits set on the mem, `OOM (out-of-memory)` will occur and terminate the pod
  * if no `requests` are set, k8 will treat the `limits` values as the `requests` value as well
  - `limit`: maximum the container can consume
    * `cpu`: expresses allocation limits in `Gi` and `m`
    * `memory`: expresses allocation limits as `G, M, K, Gi, Mi, Ki`

* Ex:

```yml
spec:
  tolerations:
    ...
  affinity:
    nodeAffinity:
      ....
  containers:
  - name: nginx
    image: nginx
    ports:
    - containerPort: 8080
      name: http-port
    resources:
      requests: 
        memory: 2Gi
        cpu: 500m
      limits:
        memory: 2Gi
        cpu: 1000m
```

<h2>Limit Ranges</h2>
 
* By default k8 does not have limits set for pods in the cluster, so we can define a `LimitRange` to solve this issue
  - `LimitRange` set default `limits` for pods in a namespace
  - `LimitRange` can be overrided by setting `limits` to a pods configuration manually, otherwise it will error
  - `LimitRange` can be set for `pods, containers, and PVC`

* `limits`
  - `default`: default `limits` for a container
  - `defaultRequest`: default `requests` for a container
  - `max`: maximum `limits` a container can have
  - `min`: minimum `requests` a container can have

* Ex:

```yml
apiVersion: v1
kind: LimitRange
metadata:
  name: resource-constraint
  namespace: default
spec:
  limits:
  - default:
      memory: 1Gi
      cpu: 500m
    defaultRequest:
      memory: 1Gi
      cpu: 500m
    max:
      memory: 1Gi
      cpu: 1Gi
    min:
      memory: 500Mi
      cpu: 100m
    type: Container
```

