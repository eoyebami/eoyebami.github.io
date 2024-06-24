<h1>Virtual Box Configuration</h1>
 

1. First download the virtual box program
2. Select new to create a new virtual machine, give it a name and select the type and a version of the OS (make sure virtualization is enabled on your computer.
3. Select a Memory Size 
4. Select a Hard Disk, can be one you downloaded from [osboxes.com](https://www.osboxes.org), you will need to extract the .vdi (virutal disk file). 
5. Go into system and modify the processor to suit the needs of the server.
6. Go to Network and set the adapter to Bridged Adapter, so your vm gets an IP address and is able to connect to the internet.

After this you can now power on the system. There are different ways to start your vm:
  * Normal Start: Will start and spin up a UI console for you
  * Headless Start: Will start but will not spin up a UI console, will need to connect via SSH
  * Detachable Start: Will start and spin up a UI console, but you can close display and vm will run in bg
Note: it is better to use the terminal in the host machine, rather than the UI console of the VM.

7. From that point you can ssh into the VM `(ssh ip-address of the VM)`
   * Check to make sure the VM has an ip address associated to it `(ip addr show: will list interfaces and ip addresses assigned to it)
   * If ip address is not assigned to the network inferface of your VM, assign one to it `(ip addr add <cidr-block> dev(device) <interface>
   * Make sure the ssh service is running
8. You can enable port forwarding to connect from the host machine to the vm (Network -> Advanced -> Port Forwarding)
   * Add the port you want the connect to go through on the host machine, and port 22 for the VM (guest machine, which is the default ssh port). 
   * Connect as such: ssh <user>@127.0.0.1(localhost) -p(port) <host-port>
