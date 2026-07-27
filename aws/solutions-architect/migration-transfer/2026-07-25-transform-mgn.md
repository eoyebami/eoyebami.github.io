## Transform MGN
- [Overview](#overview)
- [Components](#components)
    - [Source Side](#source-side)
    - [MGN Control Plane](#mgn-control-plane)
    - [Staging Area](#staging-area)
    - [Target Side](#target-side)
- [Demo](#demo)

### Overview

![alt text](images/mgn/image.png)
* AWS `Transform MGN` automates the migration of physical, virtual, and cloud servers to aws with minimal downtime
    - `mgn` performs continuous block level replication of your source servers and converts them for launch on AWS
        * allowing you to migrate servers without compatibility issues or performance disruption
    - `mgn` works across a broad range of operating systems such as window and linux distributions and supports both 1pv4 and 1pv6 network ocnfigs
        - you can replicate into a standard `AZs` or `Local Zones`
    - `mgn` uses 3 configuration templates to control how servers are replicated, launched, and configured after migration
        1. replication
        2. launch
        3. post-launch
    - `mgn` can manage migrations at scale by grouping servers into applications and applications into waves
        - configuration changes and actions such as launch, cutover, and archival can be performed at the server, application, or wave level go

### Components

![alt text](images/mgn/image-1.png)

#### Source Side
* `Replication Agent`: the software you install on each source server
    - it does a block level read on the disks, performs the initial full sync, and continuously captures and ships deltas
    - it opens 443 and 1500 outbound ports 
* `Source Server`: once the agent registers, each machine shows up in the `mgn` console as a `source server record`
    -  this is the unit you manage
        * it tracks replication status, lifecycle state (not started, initial sync, healthy, cutover complete), and holds the launch settings for that machine
        * `mgn` provides 90 days of continuous server replicatoin free of charge for each source server
* `Ports`:
    - `443`: outbound to the `mgn service api`, `s3`, or `private vpc endpoints` (private path)
    - `1500`: outbound to `replication servers` in the staging subnet
        * all encrypted

#### MGN Control Plane

* `MGN Service/api`: regional control plane (`mgn.<region>.amazonaws.com`) the agents talk to over 443
    - orchestrates everything
        * agent commands
        * provisioning the replication servers
        * tracking state
        * triggering launches
* `Replication Settings Template`: account/region level defaults that govern how replication happens
    - defines:
        * staging subnet to use
        * replication server instance types
        * ebs volume types
        * encryption
        * whether to use dedicated or shared replication server
        * public vs private ip connectivity
* `Launch Settings/Launch Templates`: the per-server and default configuration for how the target instance boots
    - target instance type
    - target subnet/vpc
    - security groups
    - iam automation
    * this is backed by `ec2 launch template`, this governs the launch out

#### Staging Area

* `Replication Servers`: `ec2` instances `mgn` launches automatically into your staging subnet
    - the receive the stream data from `replication agents` at port 1500 and write it to the staging volumes
        * `mgn` sizes and manages these
* `Staging EBS Volumes`: the disks in the staging subnet that continuously absorb the replicated blockes
    - this is where the live replica actually sits between now and cutover
* `Staging Area Subnet`: the subnet, in your staging vpc, where the replication servers and staging volumes live
    - the single network location on-prem needs to reach and where the 1500/443 security group rules apply
* `Snapshots`: `mgn` takes `ebs snapshots` of the staging volumes
* `Ports`:
    - `1500`: inbound to `replication servers` from your on-prem/source server cidr blocks
        * all encrypted
    - `443`: outbound to `mgn service`, `s3`, `vpc endpoint`

#### Target Side

* `Test/Cutover Instances`: the `ec2` instances `mgn` launches from the replicated data into your `target vpc/subnet`
    - the test launches go to a throwaway subnet for validation without disrupting anything
    - `mgn` creates temp instances based on `launch settings` to verify functionality
    - cutover launches are the real thing
    * NOTE: bring your own private ip is supported
* `Conversion Server`: short-lived instance `mgn` creates automatically at launch time to make the replicated block data bootable on `ec2` (injecting drivers, adjusting bootloader, etc)

### Private Setup


```
On-prem sources
      │  (agent: 443 + 1500)
      │
   DX or VPN
      │
 Transit Gateway
      │
 Staging VPC ── Staging subnet
                 ├─ Replication servers (private IPs, no public IP)
                 ├─ Staging EBS volumes
                 ├─ Interface endpoints: mgn, ec2 (+ ssm* if used)
                 └─ S3 gateway endpoint (via route table)
      │
   (at launch) → Target VPCs / subnets  ← test & cutover instances
```

* `Private data path (port 1500)`: in the replication settings template you can define that replication servers be given private ips
* `Private control path (port 443)`: by default the `replication agent` and `replications server` reach `mgn api` over public endpoints
    - to keep in rpivate we'd need a `interface vpc endpoint` in staging vpc so calls can resolve the `private ips`
* `Routing`: 
    - the staging subnet route table points at the `tgw`
    - the on prem side has a return rout to the staging subnet over the `DX/VPN`
* `DX/VPN`: direct connect or vpn to facilitate private connection

### Demo

1. First initialize `mgn` service
    - ![alt text](images/mgn/image-2.png)
        * this will create `iam roles` `mgn` needs to function
        * ![alt text](images/mgn/image-3.png)
    
2. Add `source servers`
    - ![alt text](images/mgn/image-4.png)
    - ![alt text](images/mgn/image-5.png)
        * you can define specific disk or replicate all disks
        * `iam role` is required for server registration 
    - ![alt text](images/mgn/image-6.png)
        * aws providers installer download command
            - run commands in all on prem servers you want to replicate
    - ![alt text](images/mgn/image-24.png)
        * `mgn connectors` can automate this process for you

3. You'll see `source servers` begin to populate in `mgn` console
    - ![alt text](images/mgn/image-12.png)
    * replication lifecycle
        - ![alt text](images/mgn/image-13.png)
    * you must launch test instances or mark as ready for cutover before you cutover
        - ![alt text](images/mgn/image-18.png)
    * you'll see once the servers are ready for testing
        - ![alt text](images/mgn/image-26.png)
    * you can see server and disk info
        - ![alt text](images/mgn/image-33.png)

4. Modify Replication Template
    - ![alt text](images/mgn/image-7.png)
    - ![alt text](images/mgn/image-8.png)
    * mod replication template
        - ![alt text](images/mgn/image-9.png)
        - ![alt text](images/mgn/image-10.png)
        - ![alt text](images/mgn/image-11.png)

5. Group `source servers` into `applications`
    - ![alt text](images/mgn/image-14.png)
    - ![alt text](images/mgn/image-15.png)

6. Add `applications` to `waves`
    - ![alt text](images/mgn/image-16.png)
    - ![alt text](images/mgn/image-17.png)

7. Modify Launch Template
    - ![alt text](images/mgn/image-19.png)
    - ![alt text](images/mgn/image-20.png)
    * mod launch template
        - ![alt text](images/mgn/image-21.png)
        - ![alt text](images/mgn/image-22.png)
    * you can define launch templates that are specific to a `source server`
        - ![alt text](images/mgn/image-23.png)

8. As replication happens you'll see `ebs snapshots` populate in `ec2` console
    - ![alt text](images/mgn/image-25.png)

9. Launch test instances
    - ![alt text](images/mgn/image-27.png)
    - ![alt text](images/mgn/image-28.png)
    * you'll see `source servers` update
        - ![alt text](images/mgn/image-29.png)
    * `launch history` will update with current `job id` initializing test instances
        - ![alt text](images/mgn/image-30.png)
        - ![alt text](images/mgn/image-31.png)
            * job log
    * you'll see `conversion servers` be spun up to make migrated block data bootable
        - ![alt text](images/mgn/image-32.png)
    * you'll see test instances be created
        - ![alt text](images/mgn/image-34.png)
    * NOTE: typically you'd launch by waves, not directly for each source

10. Mark `source servers` as `Ready for cutover`
    - ![alt text](images/mgn/image-35.png)
    - ![alt text](images/mgn/image-36.png)
    * you'll see test instances be terminated
        - ![alt text](images/mgn/image-38.png)

11. Launch cutover instances
    - ![alt text](images/mgn/image-37.png)
    * you'll see update in `launch history`
        - ![alt text](images/mgn/image-39.png)
    * cutover instances will be created
        - ![alt text](images/mgn/image-40.png)

12. Finalize Cutover
    - this will disable replication
    * ![alt text](images/mgn/image-41.png)
    * ![alt text](images/mgn/image-42.png)
    * `source servers` data replication will show disconnected
        - ![alt text](images/mgn/image-43.png)

13. Archive `source servers`
    - ![alt text](images/mgn/image-44.png)
