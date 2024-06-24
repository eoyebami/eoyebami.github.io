<h1>Environment Variables</h1>
 
* There are multiple types of variables that can be passed to a pod
  - `Direct Referencing`
  - `Downward API`
  - `ConfigMaps`
  - `Secrets`

<h2>Direct Referencing</h2>
 
* Defining a variable and its value directly in the manifest file

```yml
spec:
  containers:
  - name: nginx
    image: nginx
    env:
    - name: TEST
      value: xxxxx
    - name: CALL
      value: "first-$(TEST)"
```

<h2>Downward API</h2>
 
* Exposes Pod Information to Containers as vars or volumes
* Supported Values 

  * Available via `fieldRef` for both environment variables and volumes:
    - `metadata.name`
    - `metadata.namespace`
    - `metadata.uid`
    - `metadata.annotations['<KEY>']`
    - `metadata.labels['<KEY>']`

  * Available via `fieldRef` for environment variables only:
    - `spec.servieAccountName`
    - `spec.nodeName`
    - `status.hostIP`
    - `status.hostIPs`
    - `status.podIP`
    - `status.podIPs`

  * Available via `fieldRef` for volumes only:
    - `metadata.labels`
    - `metadata.annotations`

  * Available via `resourceFieldRef` for environment variables only:
    - `resource: limits.cpu`
    - `resource: requests.cpu`
    - `resource: limits.memory`
    - `resource: requests.memory`
    - `resource: limits.hugepages-*`
    - `resource: requests.hugepages-*`
    - `resource: limits.ephemeral-storage`
    - `resource: requests.ephemeral-storage`

* Environment Variables

```yml
spec:
  containers:
  - name: nginx
    image: nginx
    env:
    - name: TEST
      valueFrom:
        fieldRef:
          fieldPath: metadata.name
    - name: CALL
      valueFrom:
        resourcefieldRef:
          containerName: "$(TEST)" # could be any container
          resource: limits.cpu
```

* Volumes

```yml
spec:
  containers:
  - name: nginx
    image: nginx
    env:
    - name: TEST
      valueFrom:
        fieldRef:
          fieldPath: metadata.name
    - name: CALL
      valueFrom:
        resourcefieldRef:
          containerName: "$(TEST)" # could be any container
          resource: limits.cpu
    volumeMounts:
    - name: podinfo
      mountPath: /etc/podinfo
  volumes:
  - name: podinfo
    downwardAPI:
      items:
      - path: "labels"
        fieldRef:
          fieldPath: metadata.labels
      - path: "annotations"
        fieldRef:
          fieldPath: metadata.annotations
```

<h2>ConfigMaps</h2>
 
* Map `ConfigMap` data as environment variables

```yml
spec:
  containers:
  - name: nginx
    image: nginx
    ports:
    - containerPort: 8080
    name: http-port
    command: ["nginx"]
    env:
    - name: USER
      valueFrom: # injects a key/value pair from the configmap as an environment variable
        configMapKeyRef:
          name: <config-map-name>
          key: <key-name>
    envFrom: # injects the entire configmap as an environment variables
    - configMapRef:
        name: <config-map-name>  
```

<h2>Secrets</h2>
 
* Map `Secret` data as environment variables

```yml
spec:
  containers:
  - name: nginx
    image: nginx
    ports:
    - containerPort: 8080
    name: http-port
    command: ["nginx"]
    env:
    - name: USER
      valueFrom: # injects a key/value pair from the secret as an environment variable
        secretKeyRef:
          name: <secret-name>
          key: <key-name>
    envFrom: # injects the entire secret as an environment variables
    - secretRef:
        name: <secret-map-name>
```

