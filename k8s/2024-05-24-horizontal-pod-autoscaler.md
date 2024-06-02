<h1>Horizontal Pod Autoscaler</h1>
* A `hpa` automatically updates a workload resource such to match demand on that resource
* `Kubernetes` implemnets `hpas` as a loop that runs by default every 15s, as per the `kube-controller-manager`
  - Each period, the `kube-controller-manager` queries the resource utilization against the metrics defined in the `hpa`
* In order for an `hpa` to fetch metrics it needs access to aggregated APIs, most commonly used on is `metrics.k8s.io`
  - `metrics.k8s.io` is an api provided by the add-on `metrics-server`
  - In short, `hpa` will not function unless `metrics-server` or an equivalent is running within your cluster

```yml
# Example with Defined Metrics
# Can also be used to define custom metrics
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: nginx
spec:
  scaleTargetRef: # define which resource this hpa should apply to, either Deployments or StatefulSets
    apiVersion: apps/v1
    kind: Deployment 
    name: nginx
  minReplicas: 1 # min pods this hpa can scale down to
  maxReplicas: 10 # max pods this hpa can scale up to
  metrics: # this sectio has replaced the targetCPUUtilizationPercentage field, but it can still be used
  - type: Resource 
    resource: 
      name: cpu
      target:
        type: Utilization
        averageUtilization: 50 # will scale up, if average cpu utlization exceeds 50%
  - type: Resource
    resource:
      name: memory
      target:
        type: AverageValue # Utilization can also be used 
        averageValue: 100Mi

# Example with target
apiVersion: autoscaling/v1
kind: HorizontalPodAutoscaler
metadata:
  name: nginx
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: nginx
  minReplicas: 1
  maxReplicas: 10
  targetCPUUtilizationPercentage: 50
  targetMemoryUtilizationPercentage: 50
```

* Its possible to also define custome metrics using prometheus, and the set an `hpa` to track those custome metrics as well
* More info on `hpas` can be found [here](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale-walkthrough/)
