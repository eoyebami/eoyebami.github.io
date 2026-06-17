## Firewalls
- [Overview](#overview)
- [NACLs](#nacls)
- [Security ]

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
* These are `stateless firewalls` so rules for both `outbound` and `inbound traffic` must be defined

### Security Groups

* `Security groups` are firewalls for individual resources. They are `stateful firewalls`, so you only need to allow the request. 

Example:

![Logo](https://www.linode.com/docs/guides/migrate-from-aws-security-groups-to-cloud-firewalls/security-group-rules-list_hu_809fae09ac876894.jpg)