<h1>Role Based Access Control</h1>
* `RBAC` works in kubernetes similarily to how `IAM` roles work in AWS
  - You define a role you bind it to an entity
<h2>RBAC: Role</h2>
* Create a `Role` object defining the desired permissions in a yaml file

```
cat <<EOF | kubectl apply -f -
apiVersion: rbac.authorization.k8s.io/vi
kind: Role
metadata:
  name: dev
  namespace: default # should be defined because roles are namespace bound
rules: 
- apiGroups: [""] # leaving it blank will denote it as core group
  resources: ["pods"]
  resourceNames: ["my-pod"]
  verbs: ["list", "get", "create", "update", "delete"] # were giving the role these action to the pod resource named "my-pod"
- apiGroups: ["apps"] # using the apps named group
  resources: ["deployments", "replicasets"]
  verbs: ["get", "list", "create", "patch", "update"]
EOF
```

<h2>RBAC: RoleBinding</h2>
* Bind the role we previously created to a user/group using a `RoleBinding` Object

```
cat <<EOF | kubectl apply -f -
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: dev-binding
subjects:
- kind: User # can be a Group or ServiceAccount as well
  name: user01
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: dev
  apiGroup: rbac.authorization.k8s.io
EOF
```

* To test these permissions you can run `kubectl auth can-i`
  - `kubectl auth can-i create deployments --namespace`: k8 will return a `yes` or `no` depending on your authorization level
<h2>RBAC: ClusterRoles</h2>
* `Role` and `RoleBinding` are only meant for namespace-scoped objects and cannot be used to grant cluster-scoped permissions
  - Example of cluster-scoped objects:
    * `nodes, pv,, pvc, clusterroles, clusterrolebindings, csr, namespaces, nodes, storageclass`
    * To see full list of non-namespaced resources run: `kubectl api-resources --namespaced=false`
* Hence `ClusterRole` and `ClusterRolebinding` must be used instead
  - NOTE: `ClusterRole` can also be used to grant access to namespace-scoped resources but at a cluster-scoped level

```
cat <<EOF | kubectl apply -f -
apiVersion: rbac.authorization.k8s.io/vi
kind: ClusterRole
metadata:
  name: Node-Admin
  namespace: default # should be defined because roles are namespace bound
rules:
- apiGroups: [""] # leaving it blank will denote it as core group
  resources: ["nodes"]
  verbs: ["get", "list", "create", "delete"]
EOF

cat <<EOF | kubectl apply -f -
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: Node-Admin-Binding
subjects:
- kind: User # can be a Group or ServiceAccount as well
  name: user01
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: ClusterRole
  name: Node-Admin
  apiGroup: rbac.authorization.k8s.io
EOF
```
