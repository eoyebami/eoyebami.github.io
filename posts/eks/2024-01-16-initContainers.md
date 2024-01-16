<h1>InitContainers</h1>
* Init containers are containers meant to run a process to completion, unlike regular containers. They run that process then exit
  - Example use cases include:
    * waiting for an external service to spin up
    * run a db upgrade before running the application
    * pulls code from a repo that will be used by main application
* Init containers are configured in a manifest file the same way they are configured for regular containers:

```
spec:
  initContainers:
  - name: init-busybox
    image: busybox
    volumeMount:
    - name: html-volume
      path: /log/
    command: ["sh", "-c", "echo 'Application Started' > /etc/html/index.html"]
  containers:
  - name: nginx
    image: ngix
    command: ["nginx"]
    ports:
    - containerPort: 8080
      name: http-port
    volumeMount:
    - name: html-volume
      path: /var/log/nginx
  volumes:
  - name: html-volume
    emptyDir: {}
