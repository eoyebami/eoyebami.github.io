<h1>Kustomize Imperative Commands</h1>
 
* `Kustomize` has a bunch of imperative commands that can make it easy to integrate with a CI/CD pipeline
* This can also come in handy when building `kustomization.yaml` files

```
KUSTOMIZE COMMANDS
KUSTOMIZE EDIT SET
kustomize edit --help # see possible arguments
kustomize edit set image <old-image>=<new-image> # replaces specified images in imported objects with new one
kustomize edit set image nginx=nginx:1.1.2 # similar to image transformer in kustomization.yaml
kustomize edit set namespace <string> # sets namespace fo objects
kustomize edit set namespace jenkins # similar to namespace transformer in kustomization.yaml
kustomize edit set label key:value # sets labels for imported objects
kustomize edit set label managed_by:Kustomize env:dev
kustomize edit set replicas <deployment-name>=<replicaCount> # sets replica count for a deployment
kustomize edit set replicas nginx-deployment=5
kustomize edit set nameprefix <prefix> # sets nameprefix for kustomization.yaml
kustomize edit set nameprefix dev
kustomize edit set nameprefix -- dev- #  set the -- so kustomize will read "-"
kustomize edit set namesuffix <suffix> # set namesuffix for kustomization.yaml
kustomize edit set namesuffix -- -dev #  set the -- so kustomize will read "-"

KUSTOMIZE EDIT ADD
kustomize edit add configmap <cm-name> --from-literal=key=value --from-file=fileName # creates cm similar to generator in kustomization.yaml
kustomize edit add configmap db-creds --from-literal=dbhost=mysqlhost --from-file=application.properties
kustomize edit add secret <secret-name> --from-literal=key=value --from-file=fileName # creates secret similar to generator in kustomization.yaml
kustomize edit add secret db-creds --from-literal=dbhost=mysqlhost --from-file=application.properties
kustomize edit add patch --kind Deployment --path patch.yaml # adds a patch to the kustomizatio.yaml, you can get more specific
kustomize edit add resource nginx-deployment.yaml # adds resource to kustomization.yaml resource array

KUSTOMIZE CREATE 
kustomize create --autodetect # autodetects all k8 resources in the current directory and adds them to resource 
kustomize create --resources /path/to/base # sets path as base to current dir kustomization.yaml
kustomzie create --resources /path/to/base --recursive # searches current dir and all subdirectories

KUSTOMIZE BUILD
kustomize build . # builds manifest files from current dir kustomization.yaml 
kustomize build --stack-trace . # builds manifest files from current dir kustomization.yaml and prints stack trace
```
