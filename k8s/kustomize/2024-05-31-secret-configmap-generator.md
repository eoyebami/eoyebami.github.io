<h1>Secret and ConfigMap Generator</h1>
* One big problem we face with secrets and configmaps in kubernetes, is that a change does not trigger a rollout restart on deployments that have those objects as dependencies
  - Once possible solution I have personally explored, is using dynamic labels or annotations in the objects, that are set to the value of the currentTimestamp
  - However `Kustomize` has a solution to this problem that is alot less tedious by demand

* How `Kustomize` solves this is by using `Generators`
  - `Kustomize` is smart enough to append the name of each created configmap/secret with a unique subset of characteters, and also append it in any object that references that configmap/secret
  - Each time a change is made to the data within the configmap/secret, `Kustomize` creates an entirely new object with a new subset of characters, this update is also made in objects that reference that configmap/secret
    * This forces a restart of those deployments
    * The only problem with this method, is that at each apply time a new secret/configmap is created and the old one remains as well
    * `kubectl apply -k k8s/ --prune -l key=value`": will prune all secrets/configmaps with the specified labels

<h2>ConfigMap Generator</h2>
* In the `kustomization.yaml` you can set a genarator, one of two ways
  - `literals`: pass inline data objects for `kustomize` to use to generate a configmap
 
    ```yml
    # kustomization.yaml
    apiVersion: kustomize.config.k8s.io/v1beta1
    kind: Kustomization
 
    configMapGenerator:
    - name: db-creds
      literals:
      - host=msqyluser
      behavior: merge # set mainly in overlays to overwrite base configs
      options:
      labels: #option to add labels to generated cm
        app-config: my-config
    ```    

  - `files`: pass separate files that `kustomize` will use to generate configmaps
 
    ```yml
    # kustomization.yaml
    apiVersion: kustomize.config.k8s.io/v1beta1
    kind: Kustomization

    configMapGenerator:
    - name: db-creds
      files:
      - application.properties # fileName will also be key in configMap 
      behavior: merge # set mainly in overlays to overwrite base configs
      options:
      labels: #option to add labels to generated cm
        app-config: my-config
   
    # application.properties
    host=mysqluser
    ```

<h2>Secret Generator</h2>
* For a secret generator, the configs are almost identical to what we use for configmap generators

 - `literals`: pass inline data objects for `kustomize` to use to generate a configmap
 
    ```yml
    # kustomization.yaml
    apiVersion: kustomize.config.k8s.io/v1beta1
    kind: Kustomization

    secretGenerator:
    - name: db-creds
      literals:
      - host=msqyluser
      behavior: merge # set mainly in overlays to overwrite base configs
      type: Opaque # type of secret can also be specified
      options:
      labels: #option to add labels to generated secret
        app-config: my-config
    ```

  - `files`: pass separate files that `kustomize` will use to generate configmaps
 
    ```yml
    # kustomization.yaml
    apiVersion: kustomize.config.k8s.io/v1beta1
    kind: Kustomization

    secretGenerator:
    - name: db-creds
      files:
      - application.properties # fileName will also be key in configMap
      type: Opaque # type of secret can also be specified
      behavior: merge # set mainly in overlays to overwrite base configs
      options:
      labels: #option to add labels to generated secret
        app-config: my-config

    # application.properties
    host=mysqluser
    ```
