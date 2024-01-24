<h1>Liveness, StartUp, and Readiness Probe</h1>
* These probes are methods used by kubernetes to determine if an application running on the pod is alive, healthy, and ready for use
  - If these probes fail, kubernetes can take action to resolve this (usually by restarting the container)
* The `livenessProbe` is used to determine if a container is healthy or not
* The `readinessProbe` is used to determine if a container is ready to accept traffic
* The `startupProbe` is used to deal with containers/apps that require additional start up time
  - in which case, you would give it the same fields as the `livenessProbe`
  - add an additional field `failureThreshold`; which is multipled with `periodSeconds` to provide additoinal startup time before the `livenessProbe` takes over to allow for faster responses to issues in the container
<h4>Probes: httpGet</h4>
* `httpGet`: uses a HTTP GET request

```
spec: 
  containers:
  - name: nginx
    image: nginx
    livenessProbe:
      initialDelaySeconds: 3 # seconds k8 should wait before performing first liveness probe
      periodSeconds: 3 # time between liveness probes
      httpGet: # specifies a HTTP-based liveness check at a path and port
        path: /healthz
        port: 80
      httpHeaders: # define headers for the http request
      - name: Custom-Header
        value: xxxxx
    readinessProbe:
      initialDelaySeconds: 3 # seconds k8 should wait before performing first readiness probe
      periodSeconds: 3 # time between readiness probes
      httpGet: # specifies a HTTP-based readiness check at a path and port
        path: /index.html
        port: 80
      httpHeaders: # define headers for the http request
      - name: Custom-Header
        value: xxxxx
```

<h4>Probes: exec</h4>
* `exec`: used to run a command to determine liveness of container

```
spec:
  containers:
  - name: nginx
    image: nginx
    livenessProbe:
      initialDelaySeconds: 3 # seconds k8 should wait before performing first liveness probe
      periodSeconds: 3 # time between liveness probes
      exec: # specifies a command to run
        command: ["cat","/tmp/healthy"]
    readinessProbe:
      initialDelaySeconds: 3 # seconds k8 should wait before performing first readiness probe
      periodSeconds: 3 # time between readiness probes
      exec: # specifies a command to run
        command: ["cat","/var/www/html/index.html"]
```      

<h4>Probes: tcpSocket</h4>
* `tcpSocket`: kubelet will try to open socket to your container at that port, established connection means it is healthy

```
spec:
  containers:
  - name: nginx
    image: nginx
    ports:
    - containerPort: 8080
      name: http-port
    livenessProbe:
      initialDelaySeconds: 3 # seconds k8 should wait before performing first liveness probe
      periodSeconds: 3 # time between liveness probes
      tcpSocket:
        port: 8080 # this can also be a named port i.e. http-port
    readinessProbe:
      initialDelaySeconds: 3 # seconds k8 should wait before performing first readiness probe
      periodSeconds: 3 # time between readiness probes
      tcpSocket:
        port: 8080 # this can also be a named port i.e. http-port
```
