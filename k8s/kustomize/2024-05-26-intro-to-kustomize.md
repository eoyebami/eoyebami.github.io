<h1>Introudction to Kustomize</h1>
* Similar to `Helm`, `Kustomize` provides a way to customize kubernetes deployment files across multiple envs, leaving only one central base config and different overlays of values to overwrite the base config
* `Kustomize` follows a specifc folder structure

  ```
  ├── base # a base directory containing all the base config
  │   ├── configMap.yaml
  │   ├── deployment.yaml
  │   ├── kustomization.yaml
  │   └── service.yaml
  └── overlays # overlays that will modify the base config
      ├── production
      │   ├── deployment.yaml
      │   └── kustomization.yaml
      └── staging
          ├── kustomization.yaml
          └── configMap.yaml
  ```

* `Kustomize` takes the base configs and the overlays and generates the manifest files that will be applied to each environment
  - Unlike `Helm`, `Kustomize` does not use a templating format like `Helm`, it strictly works with YAML files

<h2>Installation of Kustomize</h2>
* `Kustomize` provides a quick an easy way to install the binary on your system
  - `curl -s "https://raw.githubusercontent.com/kubernetes-sigs/kustomize/master/hack/install_kustomize.sh"  | bash -s -- ${version}`
* Verify its installation
  - `kustomize --version`

<h2>Kustomization.yaml File</h2>
* Within either our base or our overlays directories, there must always be a `kustomization.yaml` file 
  - This file is necessary, because it tells `Kustomize` what YAML files it is going to manage in each directory
* Define witin the `kustomization.yaml` file you will define these resources

  ```
  ├── base # a base directory containing all the base config
  │   ├── configMap.yaml
  │   ├── deployment.yaml
  │   ├── kustomization.yaml
  │   └── service.yaml
  ```
 
  ```yml
  # kustomization.yaml file
  apiVersion: kustomize.config.k8s.io/v1beta1
  kind: Kustomization

  resources:
  - configMap.yaml
  - deployment.yaml
  - service.yaml
 
  # define customizations that need to be made to the defined resources
  commonLabels: # this will add the below labels to each of the resources
    managed_by: Kustomize
  ```

* Once you have defined the `kustomization.yaml` file you can run `kustomize build .` to generate the manifests files
  - All this will do, is build the manifest files, it does not apply it
  - In order to apply these manifest files, you will need to redirect it to `kubectl apply`
    * `kustomize build . | kubectl apply -f -`
    * This also can serve to delete these objects as well by piping to `kubectl delete -f -`
  - This can also be done using a `-k` flag for kubectl
    * `kubectl apply -k .`
    * `kubectl delete -f .`

<h2>Managing SubDirectories</h2>
* In the case that you have multiple subdirectories for different apps, services, etc. You can use `Kustomize` to manage the resources in each directory

  ```
  ├── kustomization.yaml
  └── api
  │   ├── kustomization.yaml
  │   ├── deployment.yaml
  │   
  └── db 
      ├── kustomization.yaml
      ├── deployment.yaml
  ```

* We've defined a `kustomization.yaml` in each directory that has each resource in that subdirectory listed in its `kustomization.yaml` file
* Now within our parent `kustomization.yaml` instead of having to import each resource in each subdirectory, we can just reference the subdirectory
  
  ```yml
  # kustomization.yaml file
  apiVersion: kustomize.config.k8s.io/v1beta1
  kind: Kustomization

  resources:
  - api/
  - db/

  # define customizations that need to be made to the defined resources
  commonLabels: # this will add the below labels to each of the resources
    managed_by: Kustomize
  ```
