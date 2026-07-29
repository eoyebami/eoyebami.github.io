## Verified Permissions
- [Overview](#overview)

### Overview

* AWS `Verified Permissions` is a fully managed service for fine-grained authorization and permissions management inside applications
    - it lets devs externalize access control logic using opens-source `cedar policy language` to support RBAC and ABAC access models
    - app authenticates user via and ipd -> user tries to perform an action on a resource and app sends authorization request to `verfied permissions` -> service evaluates your stored cedar policies and returns a simple `allow` or `deny` decision.
    - while `cognito` handles authentication and user identity, `verified permissions` handles authorization and access control
    - charged based number of authorization requests per month