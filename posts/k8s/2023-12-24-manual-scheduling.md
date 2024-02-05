<h1>Manual Scheduling</h1>
* In the case, that your cluster does not have a scheduler or you do not want to use the default scheduler, you can manually schedule pods yourself.
* A block is set by kubernetes when a pod is created called `nodeName`: dictates which node the pod will be scheduled on
  - You can manually set this value yourself, and the pod will be assigned this node on creation time

```yml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    app: webapp
    tier: front-end
spec:
  containers:
  - name: nginx
    image: nginx
    ports:
    - containerPort: 8080
      name: http-port
  nodeName: node-1
```

  - Assigning a node to a preexisting pod is more complicated, as kubernetes does not let you edit the `nodeName` value after its created
    * You'll need to first create a `Binding` Object for that node and submit a `POST` request to the pods binding api with the data set to the target of the `Binding` object

```yml
apiVersion: v1
kind: Binding
metadata: 
  name: nginx
target:
  apiVersion: v1
  kind: Node
  name: node-1

curl -X POST -H "Content-Type:application/json" --data '{"apiVersion": "v1", "kind": "Binding", "metadata": {"name": "nginx"}, "target":{"apiVersion": "v1", "kind": "Node", "name": "node-1"}' "https://$SERVER/api/v1/namespaces/default/pods/$PODNAME/binding/"
```
