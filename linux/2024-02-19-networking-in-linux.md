<h1>Networking in Linux</h1>
* Networking in linux is important for connecting multiple devices together and allowing for proper communication
<h2>Switches</h2>
* We start with an example where we have 2 computers, how does vm A reach vm B?
  - We connect them to a switch, and the switch creates a network containing the 2 computers
  - In order to connect a device to a switch, it must first have a network interface
    * `ip link`: will display the network interfaces on a system
  - Once connect to the switch, which will have a cidr block for ips, you can assign ips to the interfaces of the devices
    * `ip addr add xxx.xxx.xxx.xxx/xx <name> eth0`: will add an ip addr to the interface
  - After which the computers will be able to reach one another
* To persist these changes across vm reboots, modify the `/etc/network_interfaces` file
<h2>Routers</h2>
* Routers are a step above switches, in that instead of connecting devices within a network, they connect devices across networks
  - In laymans terms they connect networks together
  - The routers connect to each network and also receive their own ips
<h2>Gateways</h2>
* Gateways are essentially doors that allow requests from within a network to reach the outside world
  - Without a gateway, access to the internet is impossible
    * `route`: displays route table on the machine, similar to route tables on subnets
  - You'll need to configure a route to any other networks in order to provide a gateway for devices on that network
    * `ip route add <dest-network-cidr> via <source-ip-of-router>`: creates a gateway leading to destination network through router's ip from source network
    * This will need to be run on any network that needs to access devices on other networks
* In order to route to the internet, you would create a gateway similar to one on a public subnets route table
  - `ip route add default via <source-ip-of-router>`
  - `ip route add 0.0.0.0/0 via <source-ip-of-router>`
* In the case of connecting private networks together as well as connecting one network to the internet, you would need 2 separate gateways
  - One gateway for a router connecting the private networks together
  - One gateway for a router connecting the public network to the internet
    * In which case, each device on the system would probably have 2 network interfaces; one private and one public split across 2 separate networks
<h2>Linux Machine as Router</h2>
* As we've learned earlier, you can connect this vm to 2 networks with 2 interfaces which will receive ips from each network respectively
* After which you will set up route to devices on each network which will go through the interface on the router instances to send traffic to the corresponding network
* By default, `ip_forward` is disabled on the router vm as to not allow traffic forwarding between public and private networks unless explictly allowed
  - Modify file `/proc/sys/net/ipv4/ip_forward` from `0` to `1`
  - To permanently change this value, modify `/etc/sysctl.conf` 
    * `net.ipv4/ip_forward = 1`
