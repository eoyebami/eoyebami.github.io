<h1>Helm Charts</h1>
 
* `Helm charts` are an instruction manual for working with `Helm`
  - Think of it like yaml files we use fro creating objects within kubernetes
<h2>Helm Chart Structure</h2>
 
* Each Chart will have the following structure

  ```console
  ├── hello-world-chart # top-level directory
  │   ├── templates # directory containing templatized objects
  │   └── values.yaml # values file
  |   └── chart.yaml 
  ```

<h2>Helm Charts: Values.yaml</h2>
 
* In `Helm charts` we have what are known as `templates` which itself are the objects that helm will apply
  - Within these templates we can define configurable custom "variables" which are referenced from and defined in what is known as a `values.yaml` file

<h2>Helm Charts: Chart.yaml</h2>
 
* Within a `Helm Chart` we also have what is known as a `chart.yaml`
  - A `chart.yaml` file is a metadata file used in charts that provides information about the chart
 
    ```yaml
    apiVersion: v2 
    # this did not exists for Helm 2
    # if this value is not included, Helm will function as Helm 2
    # v1 -> Helm 2
    # v2 -> Helm 3
    appVersion: 3.8.1 # version of application defined in the Helm Chart
    version: 12.1.27 # version of the Helm Chart itself
    description: xxxx 
    type: application # can be application of library
    # library charts provide utilities for creating other charts
    dependencies: # here we can define dependencies for out chart that need to be installed alongside or before deploying our main chart
    - name: mariadb
      version: 9.x.x
      repository: https://charts.bitnami.com/bitnami # (optional) URL to repository where dependent chart is located
      condition: mariadb.enabled # (optional) a value defined in the values.yaml that will determine whether or not this dependency will be installed
      tags: # (optional)
      - tag1
      - tag2
      alias: db # (optional) alias for dependent chart, you can reference this dependent chart by this alias
      imported-values: # (optional) this allows you to pass values to the dependent chart from the main chart
      - values.yaml
      - other-values.yaml
      skip-range-check: false # (optional) if set to true, Helm will skip version range check for the dependent chart
    # use helm dependency update to resolve and download dependencies listed in this chart.yaml
    keywords: # (optional) used to make it easier to find this chart in a public repository
    - application
    - wordpress
    maintainers: # (optional) info about maintainers of this chart
    - email: containers@bitnami.com
      name: Bitnami
    home: xxxx # (optional) link to homepage of chart
    icon: xxxx # (optional) link to icon of chart
    ```
