<h1>Pod Disruption Budget</h1>
* Pod Disruption Budgets are a tool you can used to limit the number of concurrent disruptions that can occur to your application in a cluster
* Two fields can be specified when defining your `Pod Disruption Budget`
  - `minAvailable`: minimum amount of pods that most be available for selected pods; can be expressed as either an integer or percent
  - `maxUnavailable`: maximum amount of pods that can be unavailable for selected pods; can be expressed as either an integer or percent
    * Both fields can be specified together in one `Pod Disruption Budget`
* Selectors are also specified to set pods which they apply to
  - Empty selector in `policy/v1beta1`: matches zero pods
  - Empter selector in `policy/v1`: matches all pods

```yml
apiVersion: policy/v1
kind: PodDisruptionBudget
metadata:
  name: zk-pdb
spec:
  minAvailable: 1
  selector:
    matchLabels:
      app: nginx
```

