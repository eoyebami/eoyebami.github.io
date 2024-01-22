<h1>TLS in Kubernetes</h1>
* Similarily to how server's and clients use tls to authenticate with one another, each individual component within the kubernetes infrastructure will also use tls to authenticate with one another
  - Clients: that will require their own keys and certificates to validate itself to servers
    * endusers # those making calls to the api-server
    * scheduler # makes api calls to api-server in order to schedule pods
    * controller-manager # makes api call to api-server to maintain desired state of objects in cluster
    * kube-proxy # monitors services and rules created
    * api-server # handles all management of the cluster, only component that communicates to the etcd server
    * kubelet # makes calls to the api-server to register nodes to the cluster and send reports to etcd through api-server, also loads and unloads pods in cluster
  - Servers: that will require their own keys and certificates to validate itself to the clients
    * etcd server # db of the cluster, api-server communicates with it to update it on cluster status and object info
    * api-server # management of cluster, all other communicates will communicate with it in other to update cluster resources on update status on etcd
    * kubelet # api-server will communicate with it to schedule pods on nodes
* For our first step we want to generate keys for our own CA, that will sign all other certificates in the clusters
  - This is so that if any cert is not signed by this CA, it will not be able to authenticate
  * `openssl genpkey -algorithm ed25519 -out ca.key 2048`: generate the CA key
  * `openssl req -new -key ca.key -subj "/CN=KUBERNETES-CA" -out ca.csr`: generate a certificate request with the certificate name "KUBERNETES-CA"
    - `CN`: common name
  * `openssl x509 -in ca.csr -signkey ca.key -out ca.crt`: self sign the cert with the private key 
* For all other kubernetes components, we will use this generated CA key to sign all certificates
  * `openssl genpkey -alogrithm ed25519 -out <component-nams>.key 2048`: generate a private key for the component
  * `openssl req -new -key <component-name>.key -subj "/CN=<component-name>" -out <component>.csr`: generate cert request for the component key
  * `openssl x509 -req -in <component>.csr -CA ca.crt -CAkey ca.key -out <component>.crt`: generate cert for component key, using ca cert and private key
  - NOTE: in order to verify one another's signature on their cert, each component will need to specify the `CA.crt` in its configuration
    * `--trusted-ca-file=/path/to/ca/cert`: for clients
    * `--client-ca-file=/path/to/ca/cert`: for servers
  - NOTE: when generating a cert for an admin user, you must specify which group the user is apart of for authorization purposes
    * `"/CN=ADMIN-USER01/O=SYSTEM:MASTER"`": give the cert a common name and assigned it to group `system:masters`
    * You can use this cert to interact with the cluster using api calls: `curl https://kube-apiserver:6443/api/v1/pods --key <private-key> --cert <user-cert> --cacert <ca-cert>`
    * You can also use a `kube-config` file that will contain all this information, so you can interact with `kubectl`
  - NOTE: all system components that are a part of the control-plane, must have their common name prefixed with `system`
    * `"/CN=SYSTEM:KUBE-SCHEDULER"`
    * `"/CN=SYSTEM:KUBE-CONTROLLER-MANAGER"`
    * `"/CN=SYSTEM:KUBE-PROXY"`
  - NOTE: `kube-apiserver` goes by many names in the cluster, so you'll need to generate a openssl.cnf file to specify all of these alternate names when generating the keys and certs

```
[alt_names]
DNS.1 = kubernetes
DNS.2 = kubernetes.default
DNS.3 = kubernetes.default.svc
DNS.4 = kubernetes.default.svc.cluster.local
IP.1 = <pod-IP> # as needed
IP.2 = <host-IP> # as needed
```

  * `openssl req -new -key apiserver.key -subj "/CN=kube-apiserver" -out apiserver.csr --config openssl.cnf`: generate csr using `openssl.cnf`
  * `openssl -req -in apiserver.csr -CA ca.cert -CAkey ca.key -CAcreateserial -out apiserver.crt -extensions V3_req -extfile openssl.cnf -days 1000`: generate cert for apiserver
- NOTE: `kube-apiserver` will also need a client cert for authenticating with `etcd server` and `kubelet`
  * you can make a client cert for each purpose
- NOTE: `kubelet` will need both a server and a client pair of certificates
  * The CN for the certs will be the name of the node: `/CN=node01`
    - For the server, this information will be stored in a `kubelet-config.yaml`
  * For the client certs for the nodes, it must include the group `system:nodes` and their CN must prefixed with `systemi:node:<node-name>` so that api-server knows which nodes are authenticating and give them the right permissions
    - `"/CN=SYSTEM:NODE:NODE01/O=SYSTEM:NODES"`
- NOTE: since `etcd` is only accessed by `api-server`, you can give it its own CA to manage its certs
<h2>Troubleshooting</h2>
* If you need to troubleshoot certificate errors in a cluster, first take note of all the certificates in the cluster
  - `openssl x509 -in <component>.crt -text -noout`: checks information concerning cert
    * check if the cert is expired
    * check if it has the wrong issuer
    * check alternate names
    * check CN
    * check group for each cert
* Check the logs
  - check to see if there are any `bad certificate` errors in the logs 
