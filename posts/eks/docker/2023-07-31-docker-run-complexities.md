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
