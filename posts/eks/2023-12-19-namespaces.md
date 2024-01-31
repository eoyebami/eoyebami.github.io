<h1>Namespaces: Kubernetes</h1>
* Namespaces allow you to abstract resources from one another by isolating them in their own virtual environments
* Ex:
  - Kubectl:

```
kubectl create namespace dev
```

  - YAML:

```yml
apiVersion: v1
kind: Namespace
metadata:
  name: dev
  labels:
    env: dev
```

  - Objects in each namespace and see and access each other through the object names
    * However, it is still possible for communication outside of the namespace
* Ex:
  - sql connection inside namespace
  * convention: `<name-of-service>`
     
```
mysql.connect("db-service")
```

  - sql connection outside namespace
  * convention: `<name-of-service>.<namespace>.<object>.cluster.local`: this works because when a service is created, a DNS entry is added in this format
    - `cluster.local`: default domain name of the k8 cluster
    - `<object>/service`: sub-domain of the k8 cluster

```
mysql.connect("db-service.dev.svc.cluster.local")
```

<h2>Namespace in other Objects</h2>
* The Namespace can be defined for an object in the metadata section of the yaml file
```yml
metadata:
  name: myapp-deploy
  namespace: dev
```

<h2>Switching between Namespaces</h2>
* You can switch between namespaces as follows:
  - `kubectl config set-context $(kubectl config current-context) --namespace=dev`: switches from the current context to the dev one
  - `kubectl get pods --all-namespaces`: will get objects from all namespaces
<h2>Namespaces: ResourceQuota</h2>
* Resource limits can be set to a namespace using the `ResourceQuota` object, very similar to how limits are set in `Deployments`
  - limits can be set on the amount of each object that exist in that namespace
    * `count/<resource>.<group>`: for 
    * `count/<resource>`:
* Ex:
  - YAML

```yml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: compute-quota
  namespace: dev
spac:
  hard:
    pods: "10"
    requests.cpu: "4"
    requests.memory: 5Gi
    requests.storage: 10Gi (across all pvc)
    limits.cpu: "10"
    limits.memory: 10Gi
    limits.storage: 20Gi
```

