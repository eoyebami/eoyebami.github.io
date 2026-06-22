## Global Accelerator
- [Overview](#overview)

### Overview

* `Global Accelerator` was created to address the limitations and latency caused by traversing the internet to reach an application (which more often than not, takes a very inefficient path)
* `Global Accelerator` solves this by using `edge locations` similar to `cloudfront` but in a way that allows the end user to traverse aws's backbone to reach a service.
    - Resulting in faster more secure speeds as the name implies
    - key differences between `cloudfront`
        * `Cloudfront` is meant for caching data at the edge
        * `Global Accelerator` is meant for routing users to edge locations so they get on aws's network to improve latency since the network is faster and more efficient than the internet
* Read notes in [loadbalancer](./2026-06-17-loadbalancers.md) doc on how they can be used with `albs` to produce static ips for whitelisting in end user infra, if needed.