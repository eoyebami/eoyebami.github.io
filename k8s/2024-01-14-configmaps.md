<h1>ConfigMaps</h1>
 
* A configmap is a way to abstract environment data such as properties files, outside of your deployment files. In this way all deployments, no matter how vast, will pull from one singular object
  - Rather than having to modify multiple deployments to update a env variable, you only need to update the configmap and do a rollout restart on all deployments to pick up the changes

* Create the config maps with the application configuration data:
  - imperative: 
    * `kubectl create configmap <cm-name> --from-literal=<key-name>=<value>  --from-literal=key-name-2=value-2`: provide the key/value pairs directly in the command
    * `kubectl create configmap <cm-name> --from-file=<key-name>=app-config.properties`: provide the key/value pairs from a properties file into the cm as a multi-lined string
  - declarative:
    * `kubectl apply -f <config-map.yaml>`
    * YAML:

```yml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  APP_COLOR: blue
  APP_MODE: prod
  APP_PROPS: |- # this pipe allows me to create a multi-line string
    db_user: xxx
    db_pass: xxx
```

* Inject the data into the pod

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
    volumeMounts:
    - name: app-properties
      mountPath: /path/in/container
  volumes:
  - name: app-properties
    configMap: # mounts the configmap as a file in the pod
      name: <config-map-name>
```
