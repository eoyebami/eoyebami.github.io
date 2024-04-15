<h1>Templatizing Helm Charts</h1>
* When it comes to creating helm charts, one thing we must understand is templatizing, which allows us to remove static values from our templates in the charts
  - Replace them with dynamic values that are referenced elsewhere, in something like a `values.yaml` file
* In helm, we what is known as a `template directive` which uses `Go templating language`
* `Templating directive` has a couple components to keep track of
  - `Root element`: `.`
  - `Objects`: objects passed into a template from the template engine
    * `Release`: objects concerning release information
    * `Chart`: objects referenced in `chart.yaml` file
    * `Capabilities`: objects concerning kubernetes cluster itself
    * `Values`: objects referenced in the `values.yaml` file

<h2>Objects</h2>
* There a various objects that can be called in a helm template, but helm has done the due diligence of including some built in objects
  - `Release`
    * `Release.Name`: references release name 
    * `Release.Namespace`: references release namespace
    * `Release.IsUpgrade`: this value is set to true if operation is an upgrade or rollback
    * `Release.IsInstall`: set to true if current operation is an install
    * `Release.Revision`: the revision number of this release
    * `Release.Service`: service rendering template, which in this case is helm
  - `Chart`
    * `Chart.Name`: references name specified in `chart.yaml` file
    * `Chart.ApiVersion`: references apiversion specified in `chart.yaml` file
    * `Chart.Version`: references version specified in `chart.yaml` file
    * `Chart.Type`: references chart type specified in `chart.yaml` file
    * `Chart.KeyWords`: references keywords specified in `chart.yaml` file
    * `Chart.Home`: references home specified in `chart.yaml` file
  - `Capabilities`
    * `Capabilities.APIVersions`: references a set of versions
    * `Capabilities.KubeVersion` & `Capabilities.KubeVerion.Version`: references kubernetes version
    * `Capabilities.KubeVersion.Major` & `Capabilities.KubeVersion.Minor`: references major and minor kubernetes version
    * `Capabilities.HelmVersion`: references helm version
    * `Capabilities.HelmVersion.GitCommit`: references helm git sha1
    * `Capabilities.HelmVersion.GitTreeState`: references helm git tree state
    * `Capabilities.HelmVersion.GoVersion`: references helm go compiler used
  - `Template`
    * `Template.Name`: namespaced file path of the current template
    * `Template.BasePath`: namespaced path of the templates directory of the current chart
  - `Values`
    * this is a dynamic root object, its own objects are case sensitive, depending on values in `values.yaml`
* Ex: Template file

  ```yml
  {% raw %}
  apiVersion: v1
  kind: Service
  metadata:
    name: {{ .Release.Name }}-svc
  spec:
    type: {{ .Values.service.type }}
    ports: 
    - port: {{ .Values.image_ports.port }}
      targetPort: {{ .Values.image_ports.name }}
      protocol: {{ .Values.image_ports.protocol }}
      name: {{ .Values.image_ports.name }}
    selector:
      app: {{ .Values.container_name }}
  ---
  apiVersion: apps/v1
  kind: Deployment
  metadata:
    name: {{ .Release.Name }}-nginx
  spec: 
    replicas: {{ .Values.replicaCount }}
    selector:
      matchLabels:
        app: {{ .Values.container_name }}
    template:
      metadata:
        labels:
          app: {{ .Values.container_name }}
      spec:
        containers:
        - name: {{ .Values.container_name }}
          image: {{ .Values.image.repository }}:{{ .Value.image.tag }}
          ports:
          - name: {{ .Values.image_ports.name }}
            containerPort: {{ .Values.image_ports.port }}
            protocol: {{ .Values.image_ports.protocol }}
  {% endraw %}
  ```

* Ex: Values.yaml

  ```yml
  replicaCount: 2
  container_name: hello-world
  image:
    repository: nginx
    tag: "1.16.0"
  image_ports:
    name: http
    port: 80
    protocol: TCP
  
  service:
    type: NodePort
  ```
