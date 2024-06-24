<h1>Kustomize Transformers</h1>
 
* `Kustomize` has transfomers that we can use to modify our manifest files

<h2>Kustomize: Common Transfomers</h2>
 
 
  ```yml
  # kustomization.yaml file
  # define common transformers
  commonLabels: # this will add the below labels to each of the resources
    managed_by: Kustomize
  
  namespace: nginx # places all resources defined in the kustomization.yaml into this namespace
  namePrefix: ez- # prefixes the name of each object with whats defined
  nameSuffix: -dev # suffixes the name of each object with whats defined

  commonAnnotations: # this will add this annotation to all resources defined
    branch: master
  ```

<h2>Kustomize: Image Transformers</h2>
 

  ```yml
  # kustomization.yaml file
  # define image transfomers
  images: # sets a new image
  - name: nginx # define image to replace
    newName: httpd # define replacement
    newTag: "latest" # will add this tag to the image, in this case, the new image
  ```

<h2>Kustomize: Replica Transformer</h2>
 

  ```yml
  # kustomization.yaml file
  # define replicas transfomers
  replicas: 
  - count: 5
    name: nginx-depl # deployment name
  ```
