<h1>Helm Cli Commands</h1>
* In `Helm` We have a list of commands used to interact with or install charts
* Before are a couple of `Helm` commands to remember when working with helm

```console
# use --help when working with helm commands or subcommands to help when using them
$ helm --help
# helm install --help

# Helm Search Command
# helm search hub [key-word]
$ helm search hub wordpress # queries artifacthub.io for charts matching key-word
# helm search repo [repo]
$ helm search repo wordpress # queries a local repos on your system
# --versions: disaplays all available versions of the matched charts
# --devel: includes charts marked as development versions 

# Helm Repo Command
# helm repo add [NAME] [URL] [FLAGS] # adds remote repo to your local
$ helm repo add bitnami https://charts.bitnami.com/bitnami
# helm repo update [REPO1] [FLAGS]
$ helm repo update bitnami # no repo specified will update all repos
# helm repo list [FLAGS]
$ helm repo list # lists all repos in your local

# Helm Pull Command
# helm pull [chart URL | repo/chartname]
# helm pull bitnami/wordpress # pulls repo in archived format
# helm pull --untar bitnami/wordpress # pulls repo in unarchived format, you can mod values.yaml directly in here instead of passing in your own with upgrade/install

# Helm Install Command
# helm install [release-name] [chart-name]
$ helm install my-wordpress bitnami/wordpress # this will install necessary objects defined in the chart as a release
# Helm Install flags
$ helm install --values values.yaml my-wordpress bitnami/wordpress # installs chart and sets custom values.yaml to be passed to templates
$ helm install --set wordpressBlogName="Helm Test",wordpressEmail="john@example.com" my-wordpress bitnami/wordpress # sets overrides for a values set in values.yaml
$ helm install --set-file [fileName] my-wordpress bitnami/wordpress # sets override file (key=value pairs) for a values set in values.yaml
$ helm install my-wordpress bitnami/wordpress --version 1.18.1  # can specify versions of charts to install
$ helm install my-wordpress ./wordpres # can specify path to charts that you created or pulled

# Helm Upgrade Command
# helm upgrade [release-name] [chart-name] [FLAGS]
$ helm upgrade my-wordpress bitnami/wordpress # updates a release in your cluster
# Helm Upgrade Flags
$ helm upgrade --install my-wordpress bitnami/wordpress --wait # updates a release and if it doesn't exist, it will install it, --wait flag will wait for its completion
$ helm upgrade --install my-wordpress bitnami/wordpress --dry-run=client # dry runs install of wordpress
$ helm upgrade --install my-wordpress bitnami/wordpress --dependency-update # installs charts and updates dependencies if they are missing
$ helm upgrade --install --values values.yaml my-wordpress bitnami/wordpress # installs chart and sets custom values.yaml to be passed to templates
$ helm upgrade --install --values values.yaml  --values.yaml override.yaml my-wordpress bitnami/wordpress # you can set multiple values.yaml, and right most file will take precendence
$ helm upgrade --install --set wordpressBlogName="Helm Test",wordpressEmail="john@example.com" my-wordpress bitnami/wordpress # sets overrides for values set in values.yaml
$ helm upgrade --install --set-file [fileName] my-wordpress bitnami/wordpress # sets override file (key=value pairs) for a values set in values.yaml
$ helm upgrade --install my-wordpress bitnami/wordpress --version 1.18.1  # can specify versions of charts to install
$ helm upgrade --install my-wordpress ./wordpres # can specify path to charts that you created or pulled

# Helm List Command
# helm list [FLAGS]
$ helm ls # lists all releases in your cluster

# Helm History Command
# helm history [release-name]
$ helm history my-wordpress # displays history of a release as well as chart information and status

# Helm Rollback Command
# helm rollback [release-name] [revision]
$ helm rollback my-wordpress 1 # rollback release to revision 1, this rollback will be stored in the metadata as a new revision
# rollbacks will not affect third-party resources or external volumes

# Helm Uninstall Command
# helm uninstall [release-name] [chart-name]
$ helm uninstall my-wordpress bitnami/ wordpress # uninstall a release created by helm

# Helm Create Command
# helm create [name]
$ helm create nginx-chart # creates skeleton for your own helm chart
```
