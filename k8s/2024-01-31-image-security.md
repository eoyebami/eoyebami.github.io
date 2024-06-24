<h1>Image Security</h1>
 
* Image names follow a specific format: `<docker-registry>/<account>/<repository>`
  - `docker.io/library/nginx`: would be the full image name for nginx
    * `docker.io/`: Docker Hub, default docker registry
    * `library/`: default path for docker's official images
    * `nginx`: repository
  - `docker.io/eoyebami/webapp:latest`: example using my own public repository
* This is different depending on CSP or if you are using a private repository to host your images
<h2>Private Repository</h2>
 
* To pull images from a private repository, you must first authenticate through the CRT: `docker login <registry> -u <user> -p`

- In order to mimic this action to allow kubernetes to pull images from a private repository, you'll need to create a `Secret` to host your credentials

  ```console
  $ kubectl create secret docker-registry regcred \
      --docker-server=docker.io # For DockerHub \
      --docker-username=<username> \
      --docker-password=<password> \
      --docker-email=<email>
  ```

- Specify the Secret under the `imagePullSecrets` field 

  ```yml
  spec:
    imagePullSecrets:
    - name: regcred
    containers:
    - name: nginx
      image: docker.io/library/nginx
  ```

