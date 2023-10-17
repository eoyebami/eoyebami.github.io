<h1>Docker Run Command</h1>
<h5>Misc</h5>
<h6>Docker Interactive Terminal</h6>
By Default Docker ignores all STDIN (standard inputs)
* Ex:
  - ` read -p Welcome! Please enter your name: $1`
  - If this input was dockerized and ran, docker will ignore the STDIN and print `Hello and Welcome !`
  - This is because docker run in this way does not have a terminal in which it can read this input from, it runs in a non-interatice mode
    * Mapping the STDIN of your host to your container using `-it` parameter when running `docker run` will resolve this issue: `docker run -it <docker-image>`
    * `i`: allows the STDIN
    * `it`: allows the prompt to be displayed on the host terminal
<h6>Docker Volume Mapping</h6>
We can persist data from a containere by mapping it to a volume (fs/file/path) within the host machine
* Ex:
  - `docker run -v /path/in/host:/path/in/container <docker-image>`
<h6>Docker inspect</h6>
We can retrieve all information about a container in json format using `docker inspect`
* Ex:
  - `docker inspect <docker-container>`
<h6>Docker: Env Variables</h6>
We can set env variables in Docker containers by using a `-e` flag when we run the container
* Ex:
  - `docker run -itd -e APP_COLOR=red --name webapp -p 8383:8080 webapp:lite`
    - After running the container we can view the env variables of the container by running `docker inspect`. Which will inspect the properties of a running container in json format, which will also include the ENV variables within the container
    - You will need to specify a `-e` for each env variable, if there are multiple
<h6>Docker: Resource Limitations</h6>
We can set limits to resources a container can use by using the `--cpus` and `--memory` flags
* Ex:
  - `docker run --cpus=.5 ubuntu` will limit the container to use up to 50% of the cpus of the host machine
  - `docker run --memory-100m ubuntu` will limit the container to use up to 100m of RAM in the host machine
