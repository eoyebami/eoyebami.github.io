<h1>Helm Installation and Configuration</h1>
* In order to install and use `Helm` on an OS, you will first need a fully function kubernetes environment
  - Once thats covered, you can continue on with the helm installation
    * You can find all the various installation methods [here](https://helm.sh/docs/intro/install/#from-script)
<h2>Helm 2 vs Helm 3</h2>
* `Helm 2` existed prior to RBAC and custome resource definitoins were introduced to kubernetes
  - `Tiller`: In order to Helm 2 to interact with Kubernetes, it needed to interact with `Tiller`
    * `Tiller` was installed in the kubernetes cluster, and acted as the middle man for interactions between `helm` and the `api-server`
  - `3-Way Strategic Merge Patch`: `Helm 2` does not support `3-Way Strategic Merge Patch`
    * Meaning, when Helm wants to rollback a change, it only compares the current revision of the chart to the previous revision
    * It will not detect any drift that was created outside of a `helm update`
    * Hence it is not smart enough to revert this drift, unless you make another minor change to the helm charts `values.yaml`
  - `Drift Retention`: any manual changes made to objects created by `Helm 2` will not be preserved once an upgrade is preformed
* `Helm 3` completed abandoned the need for `Tiller` with kubernetes introduction of RBAC
  - `Tiller`:  Helm could now do whatever the user, who called the `helm cli`, could do 
  - `3-Way Strategic Merge Patch`: `Helm 3` supports `3-Way Strategic Merge Patch`
    * Using this strategy, Helm is now smart enough to compare the previous revision, current revision, and the live state of that chart
    * `Helm 3` is smart enough to notice drift and revert these changes back to the original state
  - `Drift Retention`: `Helm 3` will preserve drift in objects configuration when an upgrade is performed
    * Only changing custom settings that were directly changed in the `values.yaml`
