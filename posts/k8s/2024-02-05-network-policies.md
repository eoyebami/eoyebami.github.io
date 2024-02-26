<h1>Network Policies</h1>
* Quick review on how networking happens in a cluster
  - When a pod comes online in a cluster, it gets added into an internal virtual network where they can all see one another
  - In order to allow applications to flow seemlessly, we use `services` to ensure a dns is attached to a virtual lb that distributes traffic to specified endpoints
  - By default, all pods on this virtual network have are open to all ingress and egress traffic from any other pod in the cluster
    * We can restrict this access by using `Network Policies` which are similar to `security groups` in AWS

* `Network Policies` are only functionaly when a networking solution is used that supports this object
  - Networking Solutions:
    1. `kube-router`: [setup-info](https://github.com/cloudnativelabs/kube-router/blob/master/docs/generic.md)
    2. `calico`: [setup-info](https://docs.tigera.io/calico/latest/operations/calicoctl/install)
    3. `romana`: [setup-info](https://github.com/romana/romana/tree/master/containerize)
    4. `weave-net`: [setup-info](https://www.weave.works/docs/net/latest/kubernetes/kube-addon/)

* YAML:

```yml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: network-policy
  namespace: default
spec:
  podSelector: # used to group a set a pods that will fall under the network policies
    matchLabels:
      type: db
  policyTypes: # the policy type must be specified here in order for restriction to be effection
  - Ingress
  - Egress
  ingress: # defines policy for inbound traffic
  - from: # each item in this list represents an or statement
    - ipBlock: # allows ingress traffic from pod within this cidr block except the one specified
        cidr: 172.17.0.0/16
        except:
        - 172.17.1.0/24
    - namespaceSelector: # allows all ingress traffic from pods within the webapp namespace that have the specified labelts
        matchLabels:
          name: webapp
      podSelector:
        matchLabels: 
          name: webapp
    - podSelector: # allows all ingress traffic from pods with the following label
        matchLabels:
          type: nginx
    ports: # defines which port the connections are allowed to listen to, will hit everything in the from section
    - protocol: TCP
      port: 3306
  egress: # defines policy for outbound traffic
  - to:
    - ipBlock: # allows outbound traffic to pods within the following cidr
        cidr: 10.0.0.0/24
    ports: # defines which port the connections can listen to
    - protocol: TCP
      port: 5978
  - to:
    - podSelector:
        matchLabels:
          type: nginx
    ports: # specifies egress port for specific podSelectory
    - protocol: TCP
      port: xxx
  - port: # specifies universal egress ports
    - port: 53 # for dns resolving 
      protocol: UDP
    - port: 53
      protocol: TCP
```

