<h1>Cert Manager Certificates</h1>
* In `cert-manager` a `Certificate` resource represents a csr
  - `cert-manager` uses the input of the `Certificate` object to generate both a private key and a csr
  - `cert-manager` will then use the key and csr to retrieve a signed `Certifcate` from an `Issuer` in the form of a `Secret` object
    * More info on `Issuers` can be found [here](https://eoyebami.github.io/k8s/cert-manager/2024-05-01-cert-manager-issuers.md.html)

<h2>Certificate Components</h2>
* There are a couple component that should be kept in mind when configuring the spec `Certificate` object
  - `secretName`: the tls secret object that will be generated containing the tls keys and certs
  - `secretTemplate`: # template that the generated secret will follow
    * `annotations`: define annotations
    * `labels`: define labels
  - `additionalOutputFormats`: additions for secret outputs
    * `type: CombinedPEM`: includes a `tls-combined.pem` secret in the outputted tls secret
    * `type: DER`: inlcudes a `key.der` binary format of key in outputted tls secret
  - `duration`: define when the cert should expire
    * default is 2160h or 90days
  - `renewBefore`: define when `cert-manager` should renew the cert
    * default is 360h or 15days
  - `subject`: subject that should be added when creating csr
    * `organization`: add org in subject
  - `commonName`: CN of cert, (deprecated)
  - `isCA`: is this cert to take on the role of CA cert, which will sign all other requested certs
    * default is false
  - `privateKey`: define how the private key will be generated
    * `algorithm`: algorithm to use; default is `RSA`
    * `encoding`: encoding format of the key; default is `PKCS1`
    * `size`: bit size; default `2048`
    * `rotationPolicy`: either `Never` to reuse same key for each issuance or `Always` to rotate key each time
  - `usages`: define what this certificate can be used for
    * `server auth`: indicates cert can be used for server authentication
    * `client auth`: indicates cert can be used for client authentication
  - `dnsNames`: list of DNS Names to associate with cert
  - `ipAddresses`: list of ip addresses to associate with cert
  - `issuerRef`: reference which issuer should govern over this certificate
    * `name`: name of issuer
    * `kind`: either `ClusterIssuer` or `Issuer`
    * `group`: `cert-manager.io`
  - `nameConstraints`: enabled by adding `--feature-gates=NameConstraints=true` to cert-manager controller and webhook components
    * `critical`: if set to `true` cert validation process will fail if constraints aren't satisfied
    * `permitted`: specifies names that are pemitted to be included in cert subject or altNames; define lists of `dnsDomains, ipRanges, or emailAddress` that are permitted
    * `excluded`; specifies names that are excluded in the cert subject or altNames; define lists of `dnsDOmains, ipRanges, or emailAddress` that are excluded
 
    ```yaml
    # generates a cert that will be validated by my issuer
    apiVersion: cert-manager.io/v1
    kind: Certificate
    metadata: 
      name: myingress-cert
      namespace: istio-system
    spec:
      secretName: myingress-cert
      subject:
        organizations:
        - dataengine
      dnsNames:
      - dataengine.com
      issuerRef:
        name: godaddy-issuer
        kind: ClusterIssuer
    ```

* More info about certificates [here](https://cert-manager.io/docs/usage/certificate/)
