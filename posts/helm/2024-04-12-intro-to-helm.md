<h1>Introduction to Helm</h1>
* `Helm` is a package manager for kubernetes
  - `Helm` treats each objects as part of a whole
  - With `Helm` you are able to manage kubernetes applications by packaging them into charts
  - These charts are a collection of files that describe a set of kubernetes objects that are needed to run a particular application
* We have what is called a `values.yaml` file which will contain custom settings that are parsed into the charts are apply time
* Ex:

  ```console
  # this will pull the wordpress helm repo/package and create all necessary objects defined in the repo   
  $ helm install wordpress

  # use helm to update package with any new changes to custom settings
  $ helm update wordpress

  # rollback any changes made to helm repo
  $ helm rollback wordpress

  # uninstall app created by helm repo
  $ helm uninstall wordpress
  ```

<h2>Helm Components</h2>
