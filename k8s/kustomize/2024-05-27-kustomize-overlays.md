<h1>Kustomize Overlays</h1>
* As we learned earlier, `Kustomize` follows a specific directory structure

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

* Now coming to the topic of overlays, in `Kustomize`, the way we get it to function for different environments, would be by defining patches that will work to reconfigure the manifest files for each specific environment
  - More on [Patches](https://eoyebami.github.io/k8s/kustomize/2024-05-27-kustomize-patches.html)
* In overlays, we would basically be running the `kustomize build` or the `kubectl apply -k` from with the environment defined in the overlays directory

* Here in the `base/`, we have a `kustomization.yaml` file defined that imports all the base resources

  ```yml
  # base/kustomization.yaml file
  apiVersion: kustomize.config.k8s.io/v1beta1
  kind: Kustomization

  resources:
  - configMap.yaml
  - deployment.yaml
  - service.yaml
  ```

* In the `overlays/staging`, we have a `kustomization.yaml` file defined that imports the base directory and we also define a patch to modify the important base resources

  ```yml
  # overlays/stage/kustomizaion.yaml file
  apiVersion: kustomize.config.k8s.io/v1beta1
  kind: Kustomization

  bases: # define base directory containing default resources
  - ../../base

  resources:
  - deployment.yaml

  patches:
  - target:
      kind: Deployment
      name: base-deployment
    patch: |- # modifying replicas in base deployment.yaml for staging env
      - op: replace
        path: /spec/replicas
        value: 1
  ```
