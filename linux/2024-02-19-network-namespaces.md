<h1>Network Namespaces</h1>
* Similar to how containers are spun in their own namespaces to isolate their environment from the host env and its processes, we can also create isolated network environments known as network namespaces
  - `ip netns add <netns-name>`: creates a netns
  - `ip netns`: lists all network namespaces
  - `ip netns exec <netns-name> ip link`: lists all the interfaces on that netns
    * `ip -n <netns-name> link`: will also work the same
  - `ip netns exec <netns-name> route`: lists routes in that netns
  - `ip link add veth-<netns-name> type veth peer name veth-blue`: creates two linked veth interfaces
  - `ip link set veth-<netns-name> netns <netns-name>`: binds the veth to the netns
  - `ip -n <netns-name> addr add xxx.xxx.xxx.xxx dev veth-<netns-name>`: adds an ip addr to the veth interface of that netns
  - `ip -n <netns-name> link set veth-<netns-name> up`: sets up the veth in that netns
* Now once this process is completed, the two netns should be aware of one another
  - `ip netns exec <netns-name> arp`: maps ip address of pair netns to mac address
* To delete a veth run `ip -n red link del veth-<netns-name>`
<h2>Virtual Switches</h2>
* In the case of connecting multiple network namespaces to one another, you'll need to create a virtual switch
  - `ip link add v-net-0 type bridge`: creates a virtual bridge network as a network interface in the host machine
  - `ip link set dev v-net-0 up`: sets the virtual bridge network up
  - `ip link add veth-<netns-name> type veth peer name veth-<netns-name>-br`: creates a virtual cable (two linked network veths)
  - `ip link set veth-<netns-name> netns <netns-name>`: connect one end of the veth pait to the netns
  - `ip link set veth-<netns-name>-br master v-net-0`: connect the other end of the cable to the virtual bridge network
  - `ip -n <netns-name> addr add xxx.xxx.xxx.xxx dev <netns-name>`: give an ip to the veth attached to that netns
  - `ip -n <netns-name> link set veth-<netns-name> up`: set up the veth in that netns
  - `ip addr add xxx.xxx.xxx.xxx/xxx dev v-net-0`: add a ip to the virtual bridge network interface
    * Now we can ping the other network namespaces from the host machine
<h2>Route Netns to External Networks</h2>
* In the case you want to access devices on other networks, you'll also need to create a virtual gateway on the network namespaces route table
  - `ip netns exec <netns-name> ip route add xxx.xxx.xxx.xxx/xx via <br-network-ip-addr>`: routes traffic to specified ip through the bridge networks ip on the host machine
  - `ip netns exec <netns-name> ip route add 0.0.0.0 via <br-network-ip-addr>`: routes traffic to specified ip through the bridge networks ip on the host machine
* With this, we are able to reach out to external networks but they cannot reach back because the ip of the netns is private and inaccessible from the outside world
<h4>NAT</h4>
* In order to solve this issue, we must enable a nat that will act as the requester for all traffic sent from a netns
  - `iptables -t nat -A POSTROUTING -s <br-network-cidr>/24 -j MASQUERADE`: create a new rule in the host iptable to masquerade the from address from all packets coming from the netns
<h2>Route External Networks to Netns</h2>
* We have one of two solutions here
  - Expose the internal private network through the hosts interface
  - Forward all traffic through a port
    * `iptables -t nat -A PREROUTING --dport 80 --to-destination <netns-veth-ip-addr> -j DNAT`: forwards all traffic hitting host at port 80 to netns veth ip addr at port 80
