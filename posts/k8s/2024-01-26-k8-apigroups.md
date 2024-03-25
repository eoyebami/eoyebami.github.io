<h1>Api Groups and Authorization Mechanisms</h1>
* `Api Groups` contain the resources and actions that can be on a resource, in layman's terms, these are the permissions
* Authorization Mechanisms allows us to dictate who and how any user can interact with these `api groups`
<h2>Apigoups</h2>
* We interact with out cluster through a series of api calls made to the `kube-api-server`, essentially `kubectl` has automated this process by allow us to make that call with a few clicks of a button
  - `kubeconfig` also plays a part in centralizing all necessary `POST` data in one singular file

* Nonetheless, when we interact with the cluster, we do it through `api groups`, more noteably the groups below which dictate cluster functionality:
  - `/api`: core group
  - `/apis`: named group

| ApiGroup  | Api Resource Groups/Version      | Resources                                                  |
| ---       | ---                              | ---                                                        |
| /api      | core/v1                          | pods, cm, secrets, events, ns, pv, pvc, services, nodes    |
| /apis     | apps/v1                          | deploy, rs, statefulset                                    |
| /apis     | certificates.k8.io/v1            | certificatesigningrequest                                  |
| /apis     | extensions/v1                    | ingress                                                    |
| /apis     | networking.k8s.io/v1             | networkpolices ingress(1.20+)                              |
| /apis     | storage.k8s.io/v1                | storageclass                                               |
| /apis     | authentication.k8s.io/v1         |                                                            |
| /apis     | rbac.authorization.k8s.io/v1     | role, clusterrole, rolebinding, clusterrolebinding         |
| /apis     | policys/v1                       | poddisruptionbudget                                        |

* Each of these resource in the `apiGroup` has a set of actions associated with them
  - `list, get, create, delete, update, watch`

* To list `api Groupds` run:
  - `curl http//<api-server>:6443 -k --key admin.key --cert admin.crt --cacert ca.crt`
  - You can also run `kubectl proxy`, then `curl http://localhost:8001 -k`
    * `kubectl proxy` will forward your request along with the necessary flags for you so you don't need to specify

* NOTE: notice how it syncs with the `apiVersion` and `kind` specified when creating a manifest file

```yml
apiVersion: v1
kind: Pod
```

<h2>Authorization Mechanisms</h2>
* Authorization Mechanisms dictate the ways an authenticated user can access a specified resources and objects in any specified `apiGroup`
* There are 4 mechanisms:
  1. `Node Authorizer`: grants access to permissions specifically required by `kubelet`; node specific permissions
     - `read`: `services, endpoints, nodes, pods`
     - `write`: `node status, pod status, events`

  2. `Attribute Based Access Control (ABAC)`: you define policy for user or groups and map them to the `api-server` configuration
     - `api-server` will need to be restarted to pick up any policy changes
     - (not recommended)

  3. `Role Based Access Control (RBAC)`: you define a role with a set of permissions for a user/group and associate the user/group to that role
     - most used method, steps for configuring in a cluster can be found [here](https://eoyebami.github.io/posts/eks/2024-01-27-role-based-access-control.html)
     - much more manageable that `ABAC`, updates can be made and reflect immediately to users

  4. `Webhook`: outsources authorization management to a thrid-party service
  5. `AlwaysAllow`: authorizes all requests
  6. `AlwaysDeny`: denys all requests
* These 4 mechanisms are defined in the `kube-apiserver` configuration under the flag: `--authorization-mode=`
  - By default, `AlwaysAllow` will be set, if not specified
  - You can set multiple mechanisms and `api-server` will handle each request in the order the mechanisms are set
    * `--authorization-mode=Node,RBAC,ABAC`: if `node-authorizer` denys permission, then api-server will check `RBAC` and then `ABAC`
