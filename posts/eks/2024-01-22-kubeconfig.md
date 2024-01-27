<h1>KubeConfig</h1>
* This configuration file relates back to authentication via certificates; more information on this can be found [here](https://eoyebami.github.io/posts/eks/2024-01-16-kube-api-authentication.html)
  - Rudimentarily we would make calls with `kubectl` like this: `kubectl get pods --server <kube-api-server> --client-key admin.key --client-certificate admin.crt --certificate-authority ca.crt`
* Ordinarilty we use `kubeconfig` files to hold the information necessary to authenticate with `kube-apiserver`
  - `kube-apiserver address`
  - `client key`
  - `client cert`
  - `certificate authority cert`
  * This can then be specificed when running `kubectl`
    - `kubectl get pods --kubeconfig config`
    - default path is `$HOME/.kube/config`, in which case the `--kubeconfig` won't need to be specified
<h2>KubeConfig: Format</h2>
* The config file has 3 sections:
  1. `Clusters`: various kubernetes clusters you have access to
    - Will hold the api-server address and the CA cert: `--server <kube-api-server> --certificate-authority ca.crt # not in this format`
  2. `Users`: different users accounts that are used to access the cluster
    - Will hold the user key and certficate: `--client-key admin.key --client-certificate admin.crt # not in this format`
  3. `Contexts`: defines which users will access which cluster
* Ex:

```
apiVersion: v1
kind: Config
current-context: user01@my-cluster # sets a context to use
clusters: # array
- name: my-cluster
  cluster:
    certificate-authority-data: <cert-data> # can be base64encoded and placed here as well
    certificate-authority: /path/to/ca.crt # either current or above line can be used
    server: <kube-api-server-addr>:6443
contexts: # array
- name: user01@my-cluster
  contest:
    cluster: my-cluster
    user: user01
    namespace: default
users: # array
- name: user01
  user: 
    client-certificate: /path/to/admin.crt # can be bae64encoded and placed here as well
    client-key: /path/to/admin.key # can be base64encoded and placed here as well
```

