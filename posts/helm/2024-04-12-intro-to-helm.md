<h1>Introduction to Helm</h1>
* `Helm` is a package manager for kubernetes
  - `Helm` simplifies the deployment, management, and scaling of applications on a cluster by providing a convenient and centralized way of packing, sharing, and installating applications and their dependencies
  - It differs from kubernetes, which treats objects and individuals, by treating each object (defined in what is known as a chart) as parts of a whole

<h2>Helm Components</h2>
* In Helm we have a series of components that create its framework:
  - `Helm cli`: what allows us to interact with our kubernetes cluster using `helm`
  - `Helm Charts`: Helm charts are a collection of files that describe a set of kubernetes objects that are needed to run a particular application
    * We have what is called a `values.yaml` file which will contain custom settings that are parsed into the charts at apply time
    * We define objects within a chart is use what is known as templating to set configurable variables that will be centralized in a `"values.yaml"` file
    * More information on charts can be found [here](https://eoyebami.github.io/posts/helm/2024-04-14-helm-charts.md.html)
  - `Helm Repositories`: repositories for finding helm packages similarly to how you find docker images on Docker Hub
    * You can find a variety of created Helm Repositories in [ArtifactHub](https://artifacthub.io/)
  - `Release`: when a chart is applied to a cluster a release is created, which is a single installation of an application
  - `Revision`: is a snapshot of a release of an application, whenever a change is made to release via its custom configurations, a new revision is created
    * `Helm` saves metadata (data about data) of our releases and revisions directlty on the cluster as a kubernetes `secret` object
    * In this way, `Helm` is able to keep track of everything ever done is the cluster via `helm cli`, anybody anywhere can make changes without issues

