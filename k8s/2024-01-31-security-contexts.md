<h1>Security Contexts</h1>
* Similarly to how we can define a user and/or add extra privileges to that container, we can define this as well in kubernetes
  - We can configure this is a way that can impact the pod or the container level
  
  ```yml
  apiVersion: v1
  kind: Pod
  metadata: 
    name: nginx
  spec:
    securityContext: # contexts defined at the pod level
      runAsUser: 1001
    containers:
    - name: nginx
      image: nginx
      securityContext: # contexts set at the container level will override the pod level
        runAsUser: 1001
        runAsGroup: 3000
        runAsNonRoot: true # boolean param
        allowPrivilegeEscalation: false # boolean, can this container gain more privilege than the parent process
        capabilities: # can only be defined at the container level
          add: [""] # here you can define extra privileges for a container
          drop: [""] # here we can define privileges to remove from a container
  ```

* More information on capabilities can be found [here](https://eoyebami.github.io/k8s/docker/2024-01-31-docker-container-capabilities.md)
