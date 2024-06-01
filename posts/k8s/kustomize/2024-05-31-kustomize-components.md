<h1>Kustomize Componentes</h1>
* Using `Kustomize Components` you are able to define resuable pieces of confid logic that can be called in multiple overlays, similar to how functions work within any given programming language

 ```
  ├── base # a base directory containing all the base config
  │   ├── configMap.yaml
  │   ├── deployment.yaml
  │   ├── kustomization.yaml
  │   └── service.yaml
  ├── components # a components directory where we define resouces that will be imported by overlays
  │   ├── deployment.yaml
  │   └── kustomization.yaml
  └── overlays # overlays that will modify the base config
      ├── production
      │   ├── deployment.yaml
      │   └── kustomization.yaml
      └── staging
          ├── kustomization.yaml
          └── configMap.yaml
  ```

* When defining a components, it has a different api and kind

  ```yml
  # components/kustomization.yaml
  apiVersion: kustomize.configs.k8s.io/v1alpha1
  kind: Component

  resources: # import resources in the component
  - deployment.yaml

  patches: # patches can also be included to modify base directory resources
  ...
  ```

* Now we'll need to import these components into our overlays

  ```yml
  apiVersion: kustomize.configs.k8s.io/v1beta1
  kind: Kustomize

  bases:
  - ../../base
  
  components:
  - ../../components
  ```
