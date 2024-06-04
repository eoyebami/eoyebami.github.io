<h1>Commands and Arguments in Kubernetes</h1>
* This is very similar to how `ENTRYPOINT` and `CMD`, used in a Dockerfile, can be manipulated in at container runtime
  - Quick Review:
    * `ENTRYPOINT`: defines a command that will be run when a image is deployed in a container, an arg mayneed to be provided to ensure no failure (`docker run <image> 10`)
    * `CMD`: defines but a command and arg as a list, that will be run when an image is deployed in a container, if you want to override the arg, you'll need to override the full command
    - More info [here](https://eoyebami.github.io/k8s/docker/2023-08-05-dockerfile-cmd-and-entrypoint.html)

<h2>Args</h2>
* Similar to this override process in docker, you can provide args to the `ENTRYPOINT` in an image, within a kubernetes manifest file

```yml
spec:
  containers:
  - name: ubuntu-sleep
    image: ubuntu-sleep
    args: ["10"] (this arg will be appended to the entrypoint of the image; which is this case is a sleep command)
```

<h2>Command</h2>
* Similar to the override process in docker, you can provide an entire command to override that which was specified by either `CMD` or `ENTRYPOINT`, within a kubernetes manifest file

```yml
spec:
  containers:
  - name: ubuntu-sleep
    image: ubuntu-sleep
    command: ["sleep"] (overrides the ENTRYPOINT instruction)
    args: ["10"] (overrides the CMD instruction)
```
