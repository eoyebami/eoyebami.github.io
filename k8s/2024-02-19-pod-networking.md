<h1>Pod Networking</h1>
 
* Once you have an entire cluster up and ready to go, from control plane and data plane components to the architecture and node networking, k8 expects you to also have pod networking configured with certain standards
  - Every pod should have an IP address
  - Every pod should be able to communicate with every other pod in the same node
  - Every pod should be able to communicate with every other pod on other nodes without a NAT

1. A multi-node cluster configured on an external network at `192.168.1.0/20`
   * `node01: 192.168.1.11` `node02: 192.168.1.12` `node03: 192.168.1.13`
2. A bridge network is configured on each node 
   * `ip link add v-net-0 type bridge`
3. Set the status for each network to `UP`
   * `ip link set dev v-net-0 up`
   * Decide what cidr you'd like to give to each bridge network
     - `10.244.1.1` `10.244.2.1` `10.244.3.1`

4. Give each bridge network an ip addr
   * `ip addr add 10..244.n.1/24`: `n` for the thrid octet of each virtual bridge network
5. Next we'll need a script that will attach each container to the bridge network across all nodes
  
   ```console
     # create veth pair
     ip link add veth-$(NETNS) type veth peer name veth-$(NETNS)-br
     
     # attach pair to bridge and netns
     ip link set veth-$(NETNS) netns $(NETNS)
     ip link set veth-$(NETNS)-br master v-net-0
     
     # add addr to netns veth and set up
     ip -n $(NETNS) addr add $(IP) dev $(NETNS)

     # add default route from bride network
     ip -n $(NETNS) route add 0.0.0.0 via $(BRIDGE-IP)
     
     # bring up the interface
     ip -n $(NETNS) link set veth-$(NETNS) up
     
     # add gateway to route tables to expose pods from other machines in the same network
     ip route add $(NETNS-ADDR) via $(HOSTMACHINE-IP)
     # this is more easily managed by dedicating a node as a router to which all cluster nodes will go through for routing traffic
   ``` 

6. Configure script to be run automatically by the crt, by formatting it to cni standards
   ```console
     ADD)
     # create veth pair
     ip link add veth-$(NETNS) type veth peer name veth-$(NETNS)-br

     # attach pair to bridge and netns
     ip link set veth-$(NETNS) netns $(NETNS)
     ip link set veth-$(NETNS)-br master v-net-0

     # add addr to netns veth and set up
     ip -n $(NETNS) addr add $(IP) dev $(NETNS)

     # add default route from bride network
     ip -n $(NETNS) route add 0.0.0.0 via $(BRIDGE-IP)

     # bring up the interface
     ip -n $(NETNS) link set veth-$(NETNS) up

     # add gateway to route tables to expose pods from other machines in the same network
     ip route add $(NETNS-ADDR) via $(HOSTMACHINE-IP)
     # this is more easily managed by dedicating a node as a router to which all cluster nodes will go through for routing traffic
     DEL)
     ip link del veth-$(NETNS)
   ```

7. Configure crt to use this script
   * `/etc/cni/net.d/net-script.conflist`: configures name of script
   * `/opt/cni/bin/script.sh`: where script can be found

8. CRT will use this script everytime a container is brought up
   * `./sript.sh add <cid> <netns>`
