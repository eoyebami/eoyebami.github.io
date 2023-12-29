<h1>Node Selectors</h1>
* Similar to how labels on pods can be selected from by services as `pod selectors`, labels from nodes can selected from by pods as `node selectors`, to define which node a pod should be scheduled
  - Before defining the `nodeSelectors` in a pod definition, you must first label the nodeS:
    * `kubectl label nodes <node-name> <key>=<value>`
    * `kubectl label nodes node-01 size=Large` 
* Ex:

```
spec:
  containers:
  - name: nginx
    image: nginx
  nodeSelector:
    size: Large
```
