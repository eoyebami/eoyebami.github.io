## Elastic Disaster Recovery
- [Overview](#overview)
- [How it Works](#how-it-works)

### Overview

* AWS `Elastic Disaster Recover (DRS)` is a managed service that provides fast and reliable recovery of on prem and cloud based applications using
    - affordable storage
    - minimal compute
    - point in time recovery
* Essentially with a push of a button, you can have your applications failover to DR infrastructure. As this is a fully managed service
* You can failover from:
    - On prem -> AWS
    - Other CSPs -> AWS
    - From one region to another

### How it Works

* You first to need to decide what exactly needs to be monitored for failover
    - source servers
        * For these source servers, we need to be able to replicate data from them, for the day we'll need to failover to one of them
            - You need the `aws replication agent` installed
* The replication agent will need a `staging area` where of the replicated data will be sent (archived)
    - Data is replicated to a staging aread subnet in a region you select
    - Staging aread designed for low costs by using affordable torage and minimal compute resources to maintain ongoing replication
* The `recovery servers` are servers we failover to, should we need to failover
    - You'll define how they are created in a `launch template` where specifications, locations, instance type and size, region and subnet, and security groups; will be defined