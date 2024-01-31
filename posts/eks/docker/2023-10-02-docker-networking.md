<h1>Docker Networking</h1>
<h3>Bridge Network</h3>
<h4>Single Network</h4>
* By Default Docker uses a `bridge` network to connect all containers to one source where they can all communicate with one another, if required (`--links` are not needed if a user-defined network is used) . 
  - It is a private internal network, created by docker, on the host.
  - To access any of these containers on the outside world, you would only need to map the ports (`p`)
  - Ex: 
    * `docker run ubuntu`
<h4>Multiple Bridge Networks</h4>
* Here we can define multiple bridge networks and assigned containers to isolated bridge networks on the host
  - We can do this by running:

   ```console
   $ docker network create \
     --driver bridge \
     --subnet 192.68.0.0/16 \
     --gateway 192.68.0.1 \
     custom-isolated-network

   $ docker network ls (lists all networks)
   $ docker inspect <container_name> (we'll be able to see the network its attached to as well as the ip address)
   ```

<h3>None</h3>
* This attaches the container to no network, and completely isolates the container
  - Ex:
    * `docker run ubuntu --network=none`
<h3>Host Network</h3>
* Connecting the container directly to the host network, removes the abstraction (no loner isolated)
  - If you were to run a container on port 80, it will automatically map to that port on the host network, without port mapping
  - Ex:
    * `docker run ubuntu --network=host`
