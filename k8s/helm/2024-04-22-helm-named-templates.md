<h1>Helm Named Templates</h1>
 
* In helm we have what are called named templates, where you can define custom reusable components, snippets, or helper functions/templates for that project
  - In helm we can store these in files known under the name `_helpers.tpl`
    * The underscore tells helm not to consider the file to be a template file

<h2>Defining a Template</h2>
 
* Defining a template within `_helpers.tpl`

```bash
# _helpers.tpl
{% raw %}
{{- define "labels" }}
    app.kubernetes.io/name: {{ .Release.Name }}
    app.kubernetes.io/instance: {{ .Values.type }}
{{- end}}
{% endraw %}
```

```bash
# service.yaml
{% raw %}
apiVersion: v1
kind: Service
metadata:
  name: {{ .Release.Name }}-nginx
  labels:
  {{- template "labels" . }} # call the template here, and add . to pass current scope to template
...
{% endraw %}
```

* Remember to be careful with indentation because templates are passed as it written in the `_helpers.tpl`
  - `template` is an action now a function, so you can't pipe its output to another function like `indent`
  - `include` is a fucntion that can call named templates and pipe its output to another function

```bash
{% raw %}
apiVersion: v1
kind: Deployment
metadata:
  name: {{ .Release.Name }}-nginx
  labels:
  {{- template "labels" . }}
spec:
  selector:
    matchLabels: 
    {{- include "labels" . | indent 2 }} # adds indentation to template
{% endraw %}
```

