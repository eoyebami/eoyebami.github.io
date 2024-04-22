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
$ helm repo index <dir-containing-prov-and-tgz-files> --url https://<url-to-chart-repo> # generates index.yaml file for helm chart

# Helm Pull Command
# helm pull [chart URL | repo/chartname]
# helm pull bitnami/wordpress # pulls repo in archived format
# helm pull --untar bitnami/wordpress # pulls repo in unarchived format, you can mod values.yaml directly in here instead of passing in your own with upgrade/install

# COMMANDS FOR CHART INSTALLATION AND UPGRADE
# Helm Install Command
# helm install [release-name] [chart-name]
$ helm install my-wordpress bitnami/wordpress # this will install necessary objects defined in the chart as a release
$ helm install my-wordpress bitnami/wordpress --dry-run # dry run install of wordpress
$ helm install --values values.yaml my-wordpress bitnami/wordpress # installs chart and sets custom values.yaml to be passed to templates
$ helm install --set wordpressBlogName="Helm Test",wordpressEmail="john@example.com" my-wordpress bitnami/wordpress # sets overrides for a values set in values.yaml
$ helm install --set-file [fileName] my-wordpress bitnami/wordpress # sets override file (key=value pairs) for a values set in values.yaml
$ helm install my-wordpress bitnami/wordpress --version 1.18.1  # can specify versions of charts to install
$ helm install my-wordpress ./wordpress # can specify path to charts that you created or pulled
$ helm install --create-namespace -n wordress my-wordpress ./wordpress # creates release in specified namespace and creates namespace if it doesn't exist
$ helmu install --verify jenkins/jenkins # verifies chart with pubring before installing

# Helm Upgrade Command
# helm upgrade [release-name] [chart-name] [FLAGS]
$ helm upgrade my-wordpress bitnami/wordpress # updates a release in your cluster
# Helm Upgrade Flags
$ helm upgrade --install my-wordpress bitnami/wordpress --wait # updates a release and if it doesn't exist, it will install it, --wait flag will wait for its completion
# helm upgrade -i eq to helm upgrade --install
$ helm upgrade -i my-wordpress bitnami/wordpress --dry-run # dry run install of wordpress
$ helm upgrade -i my-wordpress bitnami/wordpress --dependency-update # installs charts and updates dependencies if they are missing
$ helm upgrade -i --values values.yaml my-wordpress bitnami/wordpress # installs chart and sets custom values.yaml to be passed to templates
$ helm upgrade -i --values values.yaml  --values.yaml override.yaml my-wordpress bitnami/wordpress # you can set multiple values.yaml, and right most file will take precendence
$ helm upgrade -i --set wordpressBlogName="Helm Test",wordpressEmail="john@example.com" my-wordpress bitnami/wordpress # sets overrides for values set in values.yaml
$ helm upgrade -i --set-file [fileName] my-wordpress bitnami/wordpress # sets override file (key=value pairs) for a values set in values.yaml
$ helm upgrade -i my-wordpress bitnami/wordpress --version 1.18.1  # can specify versions of charts to install
$ helm upgrade -i my-wordpress ./wordpres # can specify path to charts that you created or pulled
$ helm upgrade -i --create-namespace -n wordress my-wordpress ./wordpress # creates release in specified namespace and creates namespace if it doesn't exist
$ helm upgrade -i --verify jenkins/jenkins # verifies chart with pubring before upgrading/installing

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

# COMMANDS FOR VALIDATION AND TEMPLATING
# Helm Lint Command
# helm lint [path/to/chart]
$ helm lint ./my-wordpress-chart # goes through charts and finds any YAML formatting issues

# Helm Template Command
# helm template [release-name] [path/to/chart]
$ helm template my-wordpress ./my-wordpress-chart # parses values.yaml file into templates to display object manifest files
$ helm template my-wordpress ./my-wordpress-chart --debug # parses values.yaml and debug flag will display any parsed file that errors
# to catch all errors, running a helm upgrade --install [release-name] [chart-name] --dry-run, will catch any errors in regard to K8 syntax and will display fully formatted manifest files as well

# COMMANDS FOR CHART PACKAGING, SIGNING, AND UPLOADING
# Helm Package Command
# helm package [path/to/chart]
$ helm package ./nginx-chart # packages chart in format nginx-chart-<chart-version>.tgz 
$ helm package -sign --key 'EZ' --keyring ~/.gnupg/secring.gpg ./nginx-chart # signs anc packages helm chart

# Helm Verify Command
# helm verify [path/to/chart]
$ helm verify ./nginx-chart # verifies chart against pubring in ~/.gnupg/pubring.gpg
$ helm verify --keyring <path-to-pubring> ./nginx-chart # verifies chart against specified path to pubring

# Helm Push Command
# helm push [path/to/chart] [remote] [flags]
$ helm push <path-to-chart> <remote>
```
