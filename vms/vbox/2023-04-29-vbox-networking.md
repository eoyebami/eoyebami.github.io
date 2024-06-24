<h1>VirtualBox Networking</h1>
 

Computer systems like a laptop or server, have different ethernet interfaces to connect to a LAN network through a hub or a switch using a cable, or a wireless interfaces to connect to the network using wifi.
  - An ip addr is assigned to an interface (using a cable for hub connection and/or wifi)
By default a virtual box can have up to 4 adapters, each can have its own network interface (which is what connects to the network, and will be assigned an ip address by that network)
  - Your computer system can have more than 1 network adapter, so its possible for a system to have multiple network connections, thereby having multiple ip addr (which other hosts can reach system through said addresses)

There are 4 different adapters in a virtual boxes (which by default only the first adapter is enabled, via NAT)

<h3>Host-Only Network:</h3> 
 
  concept: you have a host machine `(192.168.1.10)` connected to a network `(192.168.1.0)`. Within this machine, you have multiple vms, that are unable to communicate with one another. 
  * Using a Host-Only Network a private network is created within the host machine `(192.168.5.0)`, in which all the virtual interfaces of the vms are connected to (the vms all get a ip addr within the host-only network cidr range, including the host machine (is part of the network, so a virtual interface is created on our host machine and connected to the host-only network, hence it receives a ip addr in this network). 
    - This enables connection between the vms in this host-only network (including the host machine)
    - However, they cannot reach or receieve information from the outside world. 
  * Create a Host-Only Network:
    - Host-Network Manger: create a new network (this creates a internal host private network, and creates a virtual interface and assigns out laptop the first ip address on this interface)
    - Then go into the network of the -> Adapter -> Host-only Adapter -> Select the virtual interface of the network (using DHCP, each vm attached to this network will be automatically assigned a ip addr). 
  * Basically a Private Subnet

<h3>Network Address Translation (NAT) Network:</h3>
 
  concept: It is extremely similar to the host-only network, in a sense, since a virtual private network will be created on the host machine. However, how the vms in the NAT network will have access to the real world through the host machine.
  * For this example, say we have a database on another server
    - A request from the vm will go through the host machine
    - The NAT engine on the host machine, will replace the source ip with its own IP, it order to send the traffic outside, and to the database on the other server.
    - When DB receives the request, it will think it came from the host machine, and send reply to request, back to the host machine
    - The host machine receives the request and the NAT engine, changes the target IP from its own IP to the vms IP(this means that the NAT engine is stateful)
  * This Address Manipulation is why this is called a Network Address Translation
  * How to:
    - Go to the VM network and create a NatNetwork 
    - Attach the NatNetwork to the VM Adapter
  * Similar to vms on a private subnet, accessing the internet through a Bastion Host in a public subnet

<h3>Network Address Translation (NAT):</h3>
 
   concept: Similar to a NAT Nework, the only difference is that there is no internal private network. So none of the vms are able to communicate with each other, but are able to communicate with the outside work (through the Network Address Translation)
   * In this case, each vm gets its own nat router, while in the NAT network the there was only one nat router and nat network that works for all vm.
     - There is more isolation between the vms 
   * Similar to each vm residing in different private subnets, and gaining access to the internet through the NAT Gateway attached to the Bastion Host that resides in a public subnet

<h3>Bridge Network:</h3>
 
   concept: Say we have a web application running on one of the vms, and we have a system on the LAN network that is trying to reach the web application. In this case we would need a bridge network. A bridge network acts as an extension of the LAN network. A bridge network does not need to be created, it always exists, and you just need to connect to it. 
   * Once the vm adapters are connected to the bridge network, they get an ip addr in the range of the LAN networks cidr.
   * They basically become devices on the LAN network, so other devices on the network are able to see and connect to the vms.
   * Devices outside of the network, can also now access the vms through the LAN netowrk
