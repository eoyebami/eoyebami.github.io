<h1>GCP Networking</h1>
First things first, GCP uses projects; which are organization units used to isolate and manage resources. Basically a namespace for GCP resources
<h3>GCP: VPC</h3>
GCP VPCs are contained in projects, and are isolated networks. Here is some information about VPC networks in GCP:
* VPC networks are not associated with a region or zone
* They can be connected to other VPC networks in different projects or organizations 
* When you create a VPC network in GCP, you don't directly specify the CIDR block for the VPC during its creation; GCP automatically assigns the VPC a CIDR:
  - For global and regional networks; the CIDR block is `10.128.0.0/9` (this allows all ips from the ranges `10.128.0.1` to `10.255.255.254`)
    * In automatic subnet creation mode: a subnet in each region are automaticall added using an ip range from the cidr
    * In custom mode, no subnets are automatically created, you have complete control over the subnets and its ip ranges
    * You can create subnets in different regions in a VPC network, with IP ranges that are outside the cidr and are completely different (they become sub-networks).
<h3>GCP: Subnets/Subnetworks</h3>
IN GCP subnets are known as subnetworks, which have their own CIDR
* Each subnetwork has its own routing table, for traffic control within and between subnetworks
<h3>GCP: Routes</h3>
GCP routes define the paths that the network traffic takes from a VM to other destinations (these can be inside or outside the network)
* NOTE: a `hop` is a device that receives the traffic and forwards it to the next hop until it reaches the final destination
* System-generated routes:

| Types                           | Hop         | Notes                                                 |
| ---                             | ---         | ---                                                   |
| System-generated default routes | igw         | Applies to whole Network                              |
| Subnet Route                    | VPC network | Applies to whole Network (Contains Cidr of subnework) |

* Custom Routes

| Types          | Hop                      | Notes                                                                                                                         |
| ---            | ---                      | ---                                                                                                                           |
| Static routes  | forwards to a static hop | Used to control routing of traffic within or between VPCs; you define a destination route and a next hop to reach destination |
| Dynamic Routes | Peer of a BGP session    | Allow for automatic adaptation to changes in network topology                                                                 |

* Peering Routes

| Types                 | Hop                       | Notes                                                            |
| ---                   | ---                       | ---                                                              |
| Peering subnet routes | Peer VPC Netowrk          | Applies to whole Network                                         |
| Peering custom routes | Next hope in Peer Network | static or dynamic route in a network connected using VPC peering |

* Policy Based Routes

| Types                 | Hop        | Notes                                                                   |                                                            
| ---                   | ---        | ---                                                                     |
| Policy-based routes   | TCP/UDP LB | matches traffic by a combination of protocol, source and dest ip ranges |

<h3>Network Tags</h3>
Are labels that you assign to a VM and other network Resources
* Firewall Rules: if you assign a network tag to a firewall rule, you can define which instances the rule applies to
  - Using `targetTags`: you can place the tag of the Instance, and the rule will apply to all VMs with that tag.  
<h3>Firewall Rules vs FireWall Policies</h3>
* Firewall Rules:
  - A single rule that defines ingress or egress (similar to the inbound or exbound rules in AWS sg)
  - Works at the network level, controlling traffic based on the source and destination ips, ports, protocols, etc
  - typically applied to individual VM instances, subnets, or targets
  - have priority values that determine the order in which they are evaluated, lower numbered rules have more priority
* Firewall Policies:
  - collection of firewall rules organized into a policy, to manage and apply a set of rules to networks or subnets (basically the entire security group, instead of individual ingress or egress rules)
