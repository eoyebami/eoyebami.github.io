<h1>KubeConfig</h1>
* This configuration file relates back to authentication via certificates; more information on this can be found [here](https://eoyebami.github.io/posts/eks/2024-01-16-kube-api-authentication.html)
* We use `kubeconfig` files to hold the information necessary to authenticate with `kube-apiserver`
  - `kube-apiserver address`
  - `client key`
  - `client cert`
  - `certificate authority cert`
  * This can then be specificed when running `kubectl`
    - `kubectl get pods --kubeconfig config`
    - default path is `$HOME/.kube/config`, in which case the `--kubeconfig` won't need to be specified
<h2>KubeConfig: Format</h2>
