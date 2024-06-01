<h1>Kustomize Patches</h1>
* `Kustomize` patches provide a method of modifying k8 configs, by allowing you to targe specific k8 resources 
* To configure a patch, the following is necessary:
  - `Operation Type`: There are 3 types of operations needed to create a patch:
    * Add
    * Remove
    * Replace
  - `Target`: resource it should be applied on, and you can match on the follow parameters:
    * `Kind`
    * `Version/Group`
    * `Name`
    * `Namespace`
    * `LabelSelector`
    * `AnnotationSelectc`
  - `Value`: what value will be added or replaced (not needed for remove operations)

* There are 2 types of ways to define a patch in `Kustomize`:
  - `JSON 6902 Patch`: With a `JSON 6902 Patch`, we'll be directly specifying the operation and the value we want to modify, its alot more surgical than the latter option
  - `Strategic Merge Patch`: Using K8 YAMl config, you basically define what you want to change, and leave out what you want to remain the same

* There are 2 differeny ways to construct the patch as well:
  - `Inline`: directly in the `kustomization.yaml` file
  - `Separately`: in another file

* We'll use the below YAML for testing:
 
  ```yml
  # deployment.yaml
  apiVersion: apps/v1
  kind: Deployment
  metadata:
    name: ez-nginx
    namespace: nginx
  spec:
    replica: 1
    selector:
      matchLabels:
        app: nginx
    template:
      metadata:
        labels:
          app: nginx
      spec:
        containers:
        - name: ez-nginx
          image: nginx
  ```

<h2>Kustomize Patches: Add </h2>
* Here is an example of doing an add operation against a deployment manifest file
* `JSON 6902`:

  ```yml
  # kustomization.yaml
  apiVersion: kustomize.config.k8s.io/v1beta1
  kind: Kustomization

  resources:
  - deployment.yaml

  patches:
  - target:
      group: apps # refers to api group of this resource
      version: v1 # refers to api group version of this resource
      kind: Deployment
      name: ez-nginx
    patch: |- # using pipe to represent an inline patch
      - op: add # define patch operation
        path: /metadata/labels/app # define what will be added, similar to jsonpath but with '/'
        value: nginx # value that will added to target
  ```

* `JSON 6902 defined in a separate file`:

 ```yml
  # kustomization.yaml
  apiVersion: kustomize.config.k8s.io/v1beta1
  kind: Kustomization

  resources:
  - deployment.yaml

  patches:
  - target:
      group: apps # refers to api group of this resource
      version: v1 # refers to api group version of this resource
      kind: Deployment
      name: ez-nginx
    path: deployment-patch.yaml # name of separate file

  # deployment-patch.yaml
  - op: add
    path: /metadata/labels/app # define label key we're adding
    value: nginx 
  ```

* `Strategic Merge Patch`:

  ```yml
  # kustomization.yaml
  apiVersion: kustomize.config.k8s.io/v1beta1
  kind: Kustomization

  resources:
  - deployment.yaml

  patches:
  - patch: |-
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: ez-nginx
      labels:
        app: nginx
  ```

* `Strategic Merge Patch defined in a separate file`:

   ```yml
  # kustomization.yaml
  apiVersion: kustomize.config.k8s.io/v1beta1
  kind: Kustomization

  resources:
  - deployment.yaml
  
  patches:
  # If target is specified here, then .metadata.name is Not Important in the separate file
  - target:
      group: apps # refers to api group of this resource
      version: v1 # refers to api group version of this resource
      kind: Deployment
      name: ez-nginx
    path: deployment-patch.yaml # name of separate file

  # deployment=patch.yaml
  apiVersion: apps/v1
  kind: Deployment
  metadata:
    name: ez-nginx
    labels:
      app: nginx
  ```

<h2>Kustomize Patches: Replace</h2>
* Here is an example of doing a replace operation against a deployment manifest file
* `JSON 6902`:

  ```yml
  # kustomization.yaml
  apiVersion: kustomize.config.k8s.io/v1beta1
  kind: Kustomization

  resources:
  - deployment.yaml

  patches:
  - target:
      group: apps # refers to api group of this resource
      version: v1 # refers to api group version of this resource
      kind: Deployment
      name: ez-nginx
    patch: |- # using pipe to represent an inline patch
      - op: replace # define patch operation
        path: /metadata/name # define what will be replaced, similar to jsonpath but with '/'
        value: nginx-ez # value that will replace target
  ```

* `JSON 6902 defined in a separate file`:

 ```yml
  # kustomization.yaml
  apiVersion: kustomize.config.k8s.io/v1beta1
  kind: Kustomization

  resources:
  - deployment.yaml

  patches:
  - target:
      group: apps # refers to api group of this resource
      version: v1 # refers to api group version of this resource
      kind: Deployment
      name: ez-nginx
    path: deployment-patch.yaml # name of separate file

  # deployment-patch.yaml
  - op: replace 
    path: /metadata/name
    value: nginx-ez 
  ```

* `Strategic Merge Patch`:

  ```yml
  # kustomization.yaml
  apiVersion: kustomize.config.k8s.io/v1beta1
  kind: Kustomization

  resources:
  - deployment.yaml

  patches:
  - patch: |-
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: ez-nginx
    spec:
      replicas: 5 # specified this is what I want to replace
  ```

* `Strategic Merge Patch defined in a separate file`:

   ```yml
  # kustomization.yaml
  apiVersion: kustomize.config.k8s.io/v1beta1
  kind: Kustomization

  resources:
  - deployment.yaml

  patches:
  # If target is specified here, then .metadata.name is Not Important in the separate file
  - target:
      group: apps # refers to api group of this resource
      version: v1 # refers to api group version of this resource
      kind: Deployment
      name: ez-nginx
    path: deployment-patch.yaml # name of separate file

  # deployment=patch.yaml
  apiVersion: apps/v1
  kind: Deployment
  metadata:
    name: ez-nginx
  spec:
    replicas: 5 
  ```

<h2>Kustomize Patches: Remove</h2>
* Here is an example of doing a remove operation against a deployment manifest file
* `JSON 6902`:

  ```yml
  # kustomization.yaml
  apiVersion: kustomize.config.k8s.io/v1beta1
  kind: Kustomization
 
  resources:
  - deployment.yaml

  patches:
  - target:
      group: apps # refers to api group of this resource
      version: v1 # refers to api group version of this resource
      kind: Deployment
      name: ez-nginx
    patch: |- # using pipe to represent an inline patch
      - op: remove # define patch operation
        path: /metadata/namespace # define what will be removed, similar to jsonpath but with '/'
        # indexes can also be used to remove a specific object "/spec/ports/0/name" removes first port in a pod
  ```

* `JSON 6902 defined in a separate file`:

 ```yml
  # kustomization.yaml
  apiVersion: kustomize.config.k8s.io/v1beta1
  kind: Kustomization

  resources:
  - deployment.yaml

  patches:
  - target:
      group: apps # refers to api group of this resource
      version: v1 # refers to api group version of this resource
      kind: Deployment
      name: ez-nginx
    path: deployment-patch.yaml # name of separate file

  # deployment-patch.yaml
  - op: remove
    path: /metadata/namespace
  ```

* `Strategic Merge Patch`:

  ```yml
  # kustomization.yaml
  apiVersion: kustomize.config.k8s.io/v1beta1
  kind: Kustomization

  resources:
  - deployment.yaml

  patches:
  - patch: |-
    apiVersion: apps/v1
    kind: Deployment
    metadata:
     name: ez-nginx
     namespace: null # setting a value to null removes it
  ```

* `Strategic Merge Patch defined in a separate file`:

   ```yml
  # kustomization.yaml
  apiVersion: kustomize.config.k8s.io/v1beta1
  kind: Kustomization

  resources:
  - deployment.yaml

  patches:
  # If target is specified here, then .metadata.name is Not Important in the separate file
  - target:
      group: apps # refers to api group of this resource
      version: v1 # refers to api group version of this resource
      kind: Deployment
      name: ez-nginx
    path: deployment-patch.yaml # name of separate file

  # deployment=patch.yaml
  apiVersion: apps/v1
  kind: Deployment
  metadata:
    name: ez-nginx
    namespace: null
  ```

<h2>Patching Lists</h2>
* Kubernetes YAML files can sometimes contain lists, for example, when we are listing containers
* To create a patch for a list, you simply would need to reference its index

<h3>Add</h3>

  ```yml
  # deployment.yaml
  ...
  spec:
    containers:
    - name: nginx
      image: nginx

  # kustomization.yaml JSON 6902
  patches:
  - target:
      kind: Deployment
      name: Not Important
    patch: |-
      - op: add
        path: /spec/template/spec/containers/- # "-" simply means, add to the end of list 
        value: # these values will replace the preexisting values at the first index
          name: haproxy
          image: haproxy

  # kustomization.yaml Strategic Merge
  patches:
  - patch: |-
      apiVersion: apps/v1
      kind: Deployment
      metadata:
        name:Not Important
      spec:
        template:
        spec:
          containers:
          - name: haproxy
            image: haproxy
  ```

<h3>Replace</h3>

  ```yml
  # deployment.yaml
  ...
  spec:
    containers:
    - name: nginx
      image: nginx

  # kustomization.yaml JSON 6902
  patches:
  - target:
      kind: Deployment
      name: Not Important
    patch: |-
      - op: replace
        path: /spec/template/spec/containers/0 # references the first index as the target
        # path: /spec/template/spec/containers/0/image will replace the image in the list
        value: # these values will replace the preexisting values at the first index
          name: haproxy 
          image: haproxy 

  # kustomization.yaml Strategic Merge
  patches:
  - patch: |-
      apiVersion: apps/v1
      kind: Deployment
      metadata: 
        name:Not Important
      spec:
        template:
        spec:
          containers:
          - name: nginx # I'll need to specify the name of the container who's image I want to change
            image: haproxy
  ```

<h3>Remove</h3>

  ```yml
  # deployment.yaml
  ...
  spec:
    containers:
    - name: nginx
      image: nginx
    - name: mysql
      image: mysql

  # kustomization.yaml JSON 6902
  patches:
  - target:
      kind: Deployment
      name: nginx-deployment
    patch: |-
      - op: remove
        path: /spec/template/spec/containers/1 # references the first index as the target

  # kustomization.yaml Strategic Merge
  patches:
  - patch: |-
      apiVersion: apps/v1
      kind: Deployment
      metadata:
        name:Not Important
      spec:
        template:
        spec:
          containers:
          - $patch: delete # using $patch operator to tell kustomize, that whatever is specified under it should be deleted
            name: mysql
  ```

* NOTE: in situations when patching a list of containers using strategic merge, make sure you at least specify the name of the container so `Kustomize` knows which one is being modified
