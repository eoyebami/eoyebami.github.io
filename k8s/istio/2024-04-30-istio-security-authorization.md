<h1>Istio Security Authorization</h1>
* With `Authorization Policies` we can define who and how a service can be accessed:
  - who: who can make requests to that service
  - how: what requests can be made to that service 
* When a request hits an envoy, its authorization engine evaluates the request against the current `Authorization Policy` to determine whether it should approve or deny the request

<h2>Authorization Policy</h2>
* `Authorization Policy` works as an a sort of acl or security group around services within a mesh
- Below are a couple components to take note of when configuring a policy:
* `selector`: use `WorkloadSelector` to `matchLabels` for proxy this policy should match
* `rule`: define `from, to, and conditions` in which this policy should apply
  - `from`: define a `source`
    * `principals`: define an array with principals this policy should apply; format should be `<TRUST_DOMAIN>/ns/<NAMESPACE>/sa/<SERVICE_ACCOUNT>`
    * `notPrincipals`: define an array with principals this policy should not apply to
    * `requestPrincipals`: define an array of request identities derived from JWT; format should be `<ISS>/<SUB>`
    * `notrequestPrincipals`: define an array with request identities deriveed from JWT that this policy should not apply to
    * `namespaces`: define an array of namespaces that this policy should apply
    * `notNamespaces`: define an array of namespaces that this policy should not apply to
    * `ipBlocks`: define a list of ip blocks either as single ip or cidr, that this policy should apply to
    * `notIpBlocks`: define a list of ip blocks this policy should not apply to
    * More info [here](https://istio.io/latest/docs/reference/config/security/authorization-policy/#Source)
  - `to`: define an `operation` to which this policy's action will apply
    * `hosts`: define an array of hosts this policy's action will apply to
    * `noHosts`: define an array of hosts this policy's action will not apply to
    * `ports`: define an array of ports this policy's action will apply to
    * `notPorts`: define an array of ports this policy's action will not apply to
    * `methods`: define an array of HTTP requests this policy's action will apply to
    * `notMethods`: define an array of HTTP requests this policy's action will not apply to
    * `paths`: define an array of HTTP request paths this policy's action will apply to
    * `noPaths`: define an array of HTTP request paths this policy's action will not apply to
    * More info [here](https://istio.io/latest/docs/reference/config/security/authorization-policy/#Rule-To)
  - `when`: condition to govern policy's action
    * `key`: list of supported keys can be found [here](https://istio.io/latest/docs/reference/config/security/conditions/)
    * `values`: define a set of values that should match key in order to satisfy condition
    * `notValues`: define a set of values that should not match key in order to satisfy condition
    * More info [here](https://istio.io/latest/docs/reference/config/security/authorization-policy/#Condition)
* `action`: action to take for matched rules 
  - `ALLOW`: allow request if the policy matches the request 
  
    ```yml
    apiVersion: security.istio.io/v1beta1
    kind: AuthorizationPolicy
    metadata:
      name: test-policy
      namespace: jenkins
    spec:
      action: ALLOW
      rules:
      - from:
        - source:
            namespaces: ["istio-system"]
        to:
        - operation:
            methods: ["GET"]
            paths: ["/login*"]
            port: ["8080]
   
    # allow all access 
    apiVersion: security.istio.io/v1beta1
    kind: AuthorizationPolicy
    metadata:
      name: test-policy
    spec:
      rules:
      - {}
    ```
 
  - `DENY`: deny request if the policy matches the request
 
    ```yml
    apiVersion: security.istio.io/v1beta1
    kind: AuthorizationPolicy
    metadata:
      name: test-policy
      namespace: jenkins
    spec:
      action: DENY
      rules:
      - from:
        - source:
            namespaces: ["istio-system"]
        to:
        - operation:
            methods: ["GET"]
            paths: ["/login*"]
            port: ["8080]

    # allow all access for selectors
    apiVersion: security.istio.io/v1beta1
    kind: AuthorizationPolicy
    metadata:
      name: test-policy
    spec:
      selector:
        matchLabels:
          app: test
    ```

  - `AUDIT`: logs event 
   
    ```yml
    apiVersion: security.istio.io/v1beta1
    kind: AuthorizationPolicy
    metadata:
      name: test-policy
      namespace: jenkins
    spec:
      action: AUDIT
      rules:
      - from:
        - source:
            namespaces: ["istio-system"]
        to:
        - operation:
            methods: ["GET"]
            paths: ["/login*"]
            port: ["8080]
    ```

  - `CUSTOM`: is request matches policy, match custom action
