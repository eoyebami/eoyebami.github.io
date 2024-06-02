<h1>Docker Networking</h1>
* There are multiple ways to run a docker container when it comes to networking on a host machine
<h2>Network: none</h2>
* Connects the docker container to no network
  - `docker run --network none nginx`: nothing can reach this container and this container can reach out to nothing as well
<h2>Network: host</h2>
* Conneccts the docker container to the hosts network
  - `docker run --network host nginx`: the container is accessible via the host on whatever port the container is running on
<h2>Network: bridge</h2>
* Connects the docker container to an internal private network which the hosts and the containers connect to
  - `docker run nginx`: default mode of docker
* When docker is installed on a machine, a bridge network is created called bridge
  - `docker network ls`: shows all networks in the docker system
  - `ip links`: will display the docker network is docker0
    * Docker does this by running the `ip link add docker0 type bridge` in the background
  - `ip addr`: will display the cidr of that internal network
* Whenever a container is created, docker will create a netns for that container and connect it to the bridge veth network
  - `ip netns`: will list netns for each corresponding docker container
    * run a `docker inspect <containerID>` and inspect the `NetworkSettings` to view the netns information
* Docker attaches the netns to the network by creating a pair of veth and attaching one end of it to the bridge network on the machine and the other end to the netns
  - `ip link`: view the veth cable connected to the bridge network
  - `ip -n <netns> link`: view the veth cable connected to the netns
* Docker also creates and attaches an ip to the netns
  - `ip -n <netns> addr`: displays ip of the netns
<h2>Docker Port Forwarding</h2>
* Since containers are running on internal private networks, docker forwards traffic from the host machine to the containers by using a NAT
  * Docker adds a rule to the iptable to forward all traffic hitting the host at a specified port to a port the container's listening to on that netns
     ```console
        iptables \
            -t nat \
            -A DOCKER \
            -j DNAT \
            --dport 8080 \
            --to-destination <netns-ip-addr>:<port>
        iptables -nvL -t nat # to list the nat rules set by docker
     ```
