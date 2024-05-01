<h1>Istioctl Commands</h1>
* Here I will be documenting a list of istioctl commands I'd like to remember for easy access

```bash
istioctl version # lists istioctl version
istioctl x precheck # preforms checks on cluster to display an issues it may have before installing istio

# Istioctl Install Command
$ istioctl install --set profile=<profile> -y # installs istio based on the profile you defined, into your cluster

# Istioctl Analyze Command
# istio analyze [FLAGS]
$ istioctl analyze # analyzes istio config in your cluster and provides feedback on any issues is notices
$ istioctl analyze --namespace <namespace> # analyzes istio config in a specified namespace
```
