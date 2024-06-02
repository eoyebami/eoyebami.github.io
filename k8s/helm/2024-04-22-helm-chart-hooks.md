<h1>Helm Chart Hooks</h1>
* Chart Hooks allow us to define pre and post action steps for helm to complete during the `install, delete, upgrade, or rollback` actions
  - Helm will wait for the steps to be completed before moving on with or completing the action
* Context:
 - Whenever we want to run any application in kubernetes we have a `Pod`, but pods run forever
   * If we have something that we only want to run once, we create what is known as a `Job`
* We use `Jobs` with specific annotations to let Helm know that this is a pre or post action step that must be taken

<h2>Defining Jobs for Chart Hooks</h2>
* You simply add the `Job` as another template in the chart and add annotations to define when it should be run
* The annotation you define is `"helm.sh/hook": <helm-action>`; below are examples of hooks you can define:
   - `pre-install`
   - `pre-delete`
   - `pre-upgrade`
   - `pre-rollback`
   - `post-install`
   - `post-delete`
   - `post-upgrade`
   - `post-rollback` 

  ```bash
  {% raw %}
  apiVersion: batch/v1
  kind: Job
  metadata:
    name: {{ .Release.Name }}-job
    annotations:
     "helm.sh/hook": pre-upgrade # this pre-upgrade annotation tells helm to run this job before any helm upgrade is performed
  ...
  {% endraw %}
  ```

<h2>Multiple Chart Hooks</h2>
* Multiple Hooks can also be set for the same action, and weights can be assigned to each hook in order to define the order
* Weights can be set for hooks using annotation `"helm.sh/hook-weight": <int-as-str>`
* Hooks can be set with the same weight, and helm will default to sorting by object type then object name

 ```bash
  {% raw %}
  apiVersion: batch/v1
  kind: Job
  metadata:
    name: {{ .Release.Name }}-job
    annotations:
     "helm.sh/hook": pre-upgrade # this pre-upgrade annotation tells helm to run this job before any helm upgrade is performed
     "helm.sh/hook-weight": "-1" # helm sorts in ascending order, so the lower the number the greater the priority
  ...
  {% endraw %}
  ```

<h2>Hook Deletion</h2>
* After a hook is applied, it will stay on as a resource in the cluster unless a deletion policy is set
* Deletion policies can be set with the annotation `"helm.sh/hook-delete-policy": <hook-state>`
* You can define the hook delete policy as follows:
  - `hook-succeeded`: deletes hook when it succeeds
  - `hook-failed`: deletes hook when it fails
  - `before-hook-createion`: will delete hook from previously ran defined helm action before creating the new hook 

  ```bash
  {% raw %}
  apiVersion: batch/v1
  kind: Job
  metadata:
    name: {{ .Release.Name }}-job
    annotations:
     "helm.sh/hook": pre-upgrade # this pre-upgrade annotation tells helm to run this job before any helm upgrade is performed
     "helm.sh/hook-weight": "-1" # helm sorts in ascending order, so the lower the number the greater the priority
     "helm.sh/hook-delete-policy": hook-succeeded
  ...
  {% endraw %}
  ```

