<h1>Docker Storage</h1>
Dockers stores information about images and containers in the path `/var/lib/docker`
<h4>Docker Images</h4>
Information about docker images are stored in `/var/lib/docker/image`
* When we build a docker image, it it built in the same amount of layers as there are stages in the Dockerfile, which are resuable when creating new images with similar steps.
  - Its important to note, that this image layer is `READ-ONLY`, meanin after building an image, you cannot change the contents of the image, unless you build a new image
<h4>Docker Containers</h4>
Information about docker containers are stored in `/var/lib/docker/containters`
* When we run a docker container, it generates a container layer about the image layers of that container
* This container layer is `READ-WRITE` available
  - Changes made in the container will be reflected in the container layer
  - Changes made to image layered files or code will be `copied-on-write`
    * Meaning rather than modifying the image layer, the file will be copied into the container layer and all changes will be made in the image layer
<h4>Docker volumes</h4>
Information about docker volumes are stored in `/var/lib/docker/volumes`
* You can create a volume by runnging `docker volume create <volume_name>`
* You can mount the volume to a container by running `docker run -v <volume_name>:/var/lib/mysql mysql`
  - This data will persist
  - If you try to mount to a volume that doesn't already exist, docker is smart enough to generate the volume and then attach it to the container
  - This is called `volume mounting`; which uses docker volumes to mount to containers
* You can mount directores to containers, as well, by specifying a path `docker run -v /path/to/dir:/var/lib/mysql mysql`
  - This is called `bind mounting`
<h5>Docker volumes: --mount</h5>
Using the `-v` flag is outdated, the new way of mounting is by using `--mount` which allows you to provide much more parameters
* Ex:
  - `docker run --mount type=bind,source=/data/mysql,target=/var/lib/mysql mysql`
    * `type`: identifies the type of mount, either `bind` or `volume`
    * `source`: identifies the path of the `bind mount` of the name of the docker `volume mount`
    * `target`: identifies the path, within the container, that you'd like to mount the volume
