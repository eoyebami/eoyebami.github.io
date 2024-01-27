<h1>Liveness, StartUp, and Readiness Probe</h1>
* These probes are methods used by kubernetes to determine if an application running on the pod is alive, healthy, and ready for use
  - If these probes fail, kubernetes can take action to resolve this (usually by restarting the container)
* The `livenessProbe` is used to determine if a container is healthy or not
* The `readinessProbe` is used to determine if a container is ready to accept traffic
* The `startupProbe` is used to deal with containers/apps that require additional start up time
  - In which case, you would give it the same fields as the `livenessProbe`
  - For cases using the `startupProbe`, configure it in a way that will require no initialDelay, increase the `failureThreshold`
    * Rather than k8 waiting for the delay before checking the probe, it will fail until it succeeds, meaning you app can start up faster that it having to always wait for the delay
    * It'll keep running the check depending on your `failureThreshold * periodSeconds` before triggering the `livenessProbe
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
      failureThreshold: 12 # how many times the probe can fail becuase k8 considers the overall check a fail and triggers a restart
      timeoutSeconds: 10 # number of seconds before probe times outs, default is 1s
      httpGet: # specifies a HTTP-based liveness check at a path and port
        path: /healthz
        port: 80
      httpHeaders: # define headers for the http request
      - name: Custom-Header
        value: xxxxx
    readinessProbe:
      initialDelaySeconds: 3 # seconds k8 should wait before performing first readiness probe
      timeoutSeconds: 10 # number of seconds before probe times outs, default is 1s
      periodSeconds: 3 # time between readiness probes
      failureThreshold: 12 # how many times the probe can fail becuase k8 considers the overall check a fail and triggers a restart
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
  terminationGracePeriodSeconds: 30 # grace period for kubelet to wait between triggering a shut down of a failed container, default is 30s, honored if failureThreshold is reached in livenessProbe
  containers:
  - name: nginx
    image: nginx
    livenessProbe:
      initialDelaySeconds: 3 # seconds k8 should wait before performing first liveness probe
      timeoutSeconds: 10 # number of seconds before probe times outs, default is 1s
      periodSeconds: 3 # time between liveness probes
      failureThreshold: 12 # how many times the probe can fail becuase k8 considers the overall check a fail and triggers a restart
      exec: # specifies a command to run
        command: ["cat","/tmp/healthy"]
    readinessProbe:
      initialDelaySeconds: 3 # seconds k8 should wait before performing first readiness probe
      timeoutSeconds: 10 # number of seconds before probe times outs, default is 1s
      periodSeconds: 3 # time between readiness probes
      failureThreshold: 12 # how many times the probe can fail becuase k8 considers the overall check a fail and triggers a restart
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
      timeoutSeconds: 10 # number of seconds before probe times outs, default is 1s
      periodSeconds: 3 # time between liveness probes
      failureThreshold: 12 # how many times the probe can fail becuase k8 considers the overall check a fail and triggers a restart
      tcpSocket:
        port: 8080 # this can also be a named port i.e. http-port
    readinessProbe:
      initialDelaySeconds: 3 # seconds k8 should wait before performing first readiness probe
      timeoutSeconds: 10 # number of seconds before probe times outs, default is 1s
      periodSeconds: 3 # time between readiness probes
      failureThreshold: 12 # how many times the probe can fail becuase k8 considers the overall check a fail and triggers a restart
      tcpSocket:
        port: 8080 # this can also be a named port i.e. http-port
```
