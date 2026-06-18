## Firewalls
- [Overview](#overview)
- [NACLs](#nacls)
- [Security Groups](#security-groups)

### Overview

* A firewalls purpose is to monitor traffic and only allow traffic permitted by a set of predefined rules
    1. `Inbound`: protects traffic going to the service
    2. `Outbound: protects traffic going away from the service

* There are different types of firewalls
    1. `Stateless Firewalls`: must be configured to allow both `inbound` and `outbound traffic`, otherwise denies all traffic
    2. `Statefull Firewalls`: are smart enough to understand which request and response are part of the same connetion. If a request is permitted then the response is also permitted

* Now in aws there are two types of firewalls that can be configured:
    1. `Network Access Control List (NACL)`
    2. `Security Groups (sg)`

### NACLs

* `NACLs` filter traffic entering and leaving a `subnet`. They do not filter traffic within a `subnet`
    - All `subnets` within a `vpc` must be associated with a `NACL`
    - A `subnet` can only be associatde with only one `NACL` but a given `NACL` can be associated to multiple `subnets`
* These are `stateless firewalls` so rules for both `outbound` and `inbound traffic` must be defined

* `NACLs` allow you to specify allow or deny for each 
    - Each rule has a rule number associated with it which determines precedence 
    - By Default, `NACLs` allow all inbound traffic

NOTE: 
* `NACLs` do not filter traffic destined to and from:
    1. AWS DNS
    2. AWS BHCP
    3. AWS EC2 metadata
    4. AWS ECS task metadata endpoints
    5. License activation for windows instances
    6. AWS Time sync service
    7. Reserved Ip addresses used by default vpc router

Example:

![Logo](https://aws.plainenglish.io/understanding-network-access-control-lists-and-security-groups-in-aws-7637ee51ce4b)

### Security Groups

* `Security groups` are firewalls for individual resources. They are `stateful firewalls`, so you only need to allow the request. 

* All `sg` rules are allow traffic, there are no deny options as by defauly `sg` deny all traffic
    - By default all `outbound traffic` is allowed

* You are also able to assign multi `sg` to a singe resource, these rules get merged at application to that resource

Example:

![Logo](https://www.linode.com/docs/guides/migrate-from-aws-security-groups-to-cloud-firewalls/security-group-rules-list_hu_809fae09ac876894.jpg)