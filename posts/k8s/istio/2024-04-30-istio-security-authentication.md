<h1>Istio Security Architecture</h1>
* We'll be diving a bit deeper into Istio's architecture to gain better knowledge on Istio's security framework
* Within `istiod` there is a CA that manages keys and certificates within the service mesh
  - This is where certificates are validated and csr's are approved
  - Everytime a request in made within the mesh, the envoy proxies request the certificate and key from the istio agent and is able to validate it against the `istiod` CA
    * The istio-agent running as a sidecar in our application pods makes sends a csr to our `istiod`
    * `Istiod` will then sign the csr with its CA and sends the cert and the key back to `istio-agent`
    * `Istio-agent` then passes these credentials to the envoy proxy
    * `Istio-agent` the monitors the expiration of the cert and this entire process repeats periodically 
    * NOTE: is a production environment, it is best practice to configure `istiod` to use your own validated CA
    * In order to do this create the `istio-system` namespace and include a secret called cacerts that contain the relevant ca secrets and `istio` will automatically pick it up to use as its CA
  - Istio's control plane distributes authentication, authorization, secure naming polices as well as certificate and keys; to all istio proxies at all times
    * This allows for `security at depth` because every since possible route, entry, or exit in the mesh has security checks

<h2>Authentication in Istio</h2>
* Authentication in Istio must be configured in a very secure way
  - Every time there is a communication between 2 services, there must be a way that both server and client are able to validate who they are to one another
* This can be done using either: 
  - `Request Authentication`
  - `Peer Authentication`

<h4>Request Authentication</h4>
* `Request Authentication` defines what request authentication methods are supported by a workload
  - `JWT Tokens`: `Request Authentication` using tokens to validate identity (JWT, Firebase, Google)
    * You can specify jwt rules that must be followed in order to authenticate
    * Usually defined for external traffic
    * More info [here](https://istio.io/latest/docs/reference/config/security/request_authentication/)
 
    ```yml
    # Request Authentication using jwtTokens
    apiVersion: security.istio.io/v1beta1
    kind: RequestAuthentication
    metadata:
      name: test-req-auth
      namespace: test # with this set, this becomes a namespace-wide policy; if this and labels are not set it becomes mesh wide
    spec:
      selector:
        matchLabels:
          app: test # with this set, this becomes a workload-specified policy
      jwtRules:
      - isssuer: "test-issuer"
        jwksUri: "example.com"
    ```

<h4>Peer Authentication</h4>
* `Peer Authentication` defines how traffic will be routes to the envoy proxy sidecar
  - With `Peer Authentication` you can mandate that `mTLS` be mandatory for a specific workload, namespace, or the entire service mesh
  - Usually defined for internal traffic, between mesh resources
    * `workload-specified policy`: targets a group of resources based on the tagging convention
    * `namespace-wide policy`: targets all resources in a specific namespace
    * `mesh-wide policy`: governs the entire service mesh 
  - In order to actually restrict access, its best to create this authentication with an `Authorization Policy` that restricts access to authenticated requests only
  - mTLS modes:
    * `UNSET`: inherit from parent, otherwise treat as `PERMISSIVE`
    * `DISABLE`: connection not tunneled via mTLS
    * `PERMISSIVE`: connection can be plaintext or mTLS tunneled
    * `STRICT`: connection must be mTLS tunneled and TLS cert must be provided
    * More info [here](https://istio.io/latest/docs/reference/config/security/peer_authentication/)
 
    ```yml
    # Peer Authentication using mTLS
    # With this, workloads cannot access our application unless mTLS is enabled
    apiVersion: security.istio.io/v1beta1
    kind: RequestAuthentication
    metadata:
      name: test-req-auth
      namespace: test # with this set, this becomes a namespace-wide policy; if this is set to istio-system it becomes mesh-wide
    spec:
      selector:
        matchLabels:
          app: test # with this set, this becomes a workload-specified policy
      mtls:
        mode: STRICT
    ```
