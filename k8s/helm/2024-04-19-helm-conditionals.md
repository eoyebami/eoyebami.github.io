<h1>Helm Conditionals, With Blocks, Ranges</h1>
 
* Here are a couple blocks you can use to create more dynamic templates in your helm charts

<h2>If Conditionals</h2>
 
* It is possible to also use conditionals for optional aspects of your templates
  - These sections will only be parsed if the condition is met

* Ex:

  ```bash
  {% raw %}
  apiVersion: v1
  kind: Service
  metadata:
    name: RELEASE-NAME-nginx
    {{- if .Values.orgLabel }} # if this value exists then run this template
    labels:
      org: {{ .Values.orgLabel }}
    {{- else if eq .Values.orgLabel "hr" }} # else if value is equal to hr
    labels:
      org: human resources
    {{- else }}
    labels:
      org: internal
    {{- end }} # without the "-" at the start of the template, helm will parse the lines this condition takes as empty lines
  {% endraw %}
  ```

* You can even use conditions to dictate whether or not a object is even created

  ```bash
  {% raw %}
  {{- if .Values.serviceAccount.create }}
  apiVersion: v1
  kind: ServiceAccount
  metadata:
    name: {{ .Release.Name }}-sa
  {% endraw %}
  ```

* To avoid the `-` in you can write the if conditional in one line if possible

<h2>With Blocks</h2>
 
* Scopes can be set using with blocks so the entire element does not have to referenced

  ```bash
  {% raw %}
  apiVesion: v1
  kind: ConfigMap
  metadata:
    name: {{ .Release.Name }}-cm
  data:
  {{- with .Values.app }}
    {{- with .ui }} # you can define a scope within a scope
  background: {{ .bg }}
  foreground: {{ .fg }}
    {{- end }}
    {{- with .db }}
  database: {{ .name }}
  connection:  {{ .conn }}  
    {{- end }}
  release: {{ $.Release.Name }} # use a "$" in order to retrieve values from outside of the with Blocks scope
  {{- end}
  {% endraw %}
  ```

* You can even use with blocks to dictate whether or not a object is even created

  ```bash
  {% raw %}
  {{- with .Values.serviceAccount.create }}
  apiVersion: v1
  kind: ServiceAccount
  metadata:
    name: {{ .Release.Name }}-sa
  {% endraw %}
  ```

<h2>Loops and Ranges</h2>
 
* We can also use loops in our templates to iterate over lists defined in our `values.yaml` file
B

  ```yml
   # values.yaml
   regions:
   - ohio
   - iowa
   - texas
  ```

  ```bash
  {% raw %}
  apiVesion: v1
  kind: ConfigMap
  metadata:
    name: {{ .Release.Name }}-cm
  data:
    regions: 
    {{- range .Values.regions }} # setting a range will also define the scope within that range
    - {{ . | quote }} # setting a dot here will iterate over ever value at ".Values.regions"
    {{- end}
  {% endraw %}
  ```

* With this you could even dynamically create sections of an object, or even the entire object itself
  
  ```yml
  # values.yaml
  items:
  - name: item1
    value: value1
  - name: item2
    value: value2
  ```

  ```bash
  {% raw %}
  apiVesion: v1
  kind: ConfigMap
  metadata:
    name: {{ .Release.Name }}-cm
  data:
    regions: |-
    {{- range .Values.items }} 
    - name: {{ .name }} 
    - value: {{ .value }}
    {{- end}
  {% endraw %}
  ```

* Iterate over key/value pairs

  ```yml
  # values.yaml
  serviceAccount:
    create: true
    name: webapp-sa
    labels:
      tier: frontend
      type: web
      mode: proxy
  ```

  ```bash
  {% raw %}
  {{- with .Values.serviceAccount.create }}
  apiVersion: v1
  kind: ServiceAccount
  metadata:
    name: {{ $.Values.serviceAccount.name }}
    labels:
      {{- range $key, $value := $.Values.serviceAccount.labels }} # saves key/value pair as vars that can be referenced later
      {{ $key }}: {{ $value }}
      {{- end }}
  {{- end }}
  {% endraw %}
  ```

<h2>Real Life Examples</h2>
 
<h4>Iterating over Named Templates</h4>
 
* Ran into an issue where I had to iterate over a list of values that would then call `Named Templates` that themselves also iterate over another group of lists

  ```bash
  {% raw %}
  {{ range .Values.myFirstList }} # iterates over a list in my function
  {{- $templateVar := printf "chart.%s" . -}} # appends objects with named template prefix
  {{- include $templateVar $ }} # calls an include on each named template
  # the $ in the include sets the "." as the scope for the include
  # that way your named template is able to call ranges from that scope without issue
  {{- end }}
  {% endraw %}
  ```

<h4>Iterate over a Nested List</h4>
 
* Ran into an issue where I needed to interate over a nested list in a `Named Template`

  ```bash
  # values.yaml
  paths:
  - path: /path/file1
    permissions:
    - ro
    - rw
    - none
  ```

  ```bash
  # helm will iterate over each permission for each path
  {% raw %}
  {{- range $path := .Values.paths }} # saved as vars to call later without scoping issues
  {{- range $perms := $paths.permissions }} 
  - paths:
      path: {{ $path }} # call path
        permissions: {{ $perms }}
  {{- end }}
  {{- end }}
  {% endraw %}
  ```

<h4>Checking if a values exists or not</h4>
 
* Wanted to confirm if a value exists in the `values.yaml` before creating an object 

  ```bash
  {% raw %}
  {{- if empty (not .Values.deployment) }}
  # place object
  apiVersion: apps/v1
  kind: Deployment
  ...
  {{- end }}
  {% endraw %}
  ```

