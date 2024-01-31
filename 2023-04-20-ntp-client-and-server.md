<h1>NTP Client and Server</h1>

The Nework Time Protocol (NTP) is a client/server application. The client software will synchronize its clock to the netwrok time server. They help to synchronize the clocks of devices on a network. Accurate timekeeping is important for many OS, because it is used to timestamp events, synchronize processes, and ensure that data is transmitted and processed in the correct order. 

* NTP runs on a hierarchy system, servers which also have servers, which also have servers. 
* NTP uses port number 123
* For time sources, in small networks, we will be using a ntp internet server ( e.g `pool.ntp.org`)
  - Larger, more secure networks, will most likely have its own dedicated time server
<h3>NTP Servers</h3>
The NTP servers provide a source of accurate time to the clients on the network. They work by regularly querying a set of reference clocks that are known to be accurate, and uses this information to adjust its own clock and provide the correct time to the client that requests it. 
  * Installing NTP Server side:
  ```console
  yum install ntp/chronyd
  vi /etc/chrony.conf
  allow <ip_addr_of_client>
  systemctl restart chronyd
  ```
  * If you want to manually update the date and time use the `ntpupdate` command: ````ntpupdate <NTPSERVERADDRESS>````

<h3>NTP Client</h3>
The NTP clients retrieve the correct date and time from the source on the networl. They work by initiating a time-request exchange with the NTP server. The client is then able to calculate the link delay and its local offset and adjust its local clock to match the clock at the servers.
  * Installing NTP client side:
    ```console
    yum install ntp/chronyd
    vi /etc/chrony.conf
    Server <ip_addr_of_server>
    systemctl restart chronyd
    sudo chronyc sources
    ```
<h3>NTP chronyd commands</h3>

NTP uses Stratum values to represent the distance from the reference clock (accuracy). Each level adds a 1 to the value, values can go from `0-15`.
  * A stratum value of 16 indicates the device is unsynchronized
  * Stratum 0 clocks do not connect over a network, they are directly connected to a time-server. 
    - That clock has a value of 0, it is the reference clock
    - All other time servers connected to that reference clock have a stratum value of 1 (known as primary time servers)
    - Servers connected to that server have a increase in stratum by 1 level, and so on and so forth
  ex:
   ```console
   MS Name/IP address         Stratum Poll Reach LastRx Last sample
===============================================================================
^? ip-ip-addr.ec2.int>     0   7     0     -     +0ns[   +0ns] +/-    0ns
^+ haka.ruselabs.com             2   6   377    61  -4880us[-4782us] +/-   49ms
^+ four10.gac.edu                2   6   377    61  +1859us[+1958us] +/-   29ms
^* 108.61.73.243                 2   6   377    60   +236us[ +335us] +/-   34ms
^+ time.richiemcintosh.com       2   6   377    61  +1099us[+1197us] +/-   41ms
^+ li1150-42.members.linode>     2   6   377    61   +106us[ +204us] +/-   61ms
  ```

