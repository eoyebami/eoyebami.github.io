<h1>CertificateSigningRequest: Authorizate User Certificate</h1>
 
* If we want to authorize a user to authenticate through api-server of a cluster, we first need to sign their cert with the CA of the cluster
  - User will generate their key and generate a csr to hand over to the admin
    * `openssl genpkey -algorithm ed25519 -out user.key 2048`
    * `openssl req -new -key user.key -subj "/CN=USER01/O=SYSTEM:MASTER" -out user.csr`
  - Once handed to the admin, they can then login to the CA server and sign the csr with the CA.key
    * `openssl x509 -req -in user.csr -CA ca.crt -CAkey ca.key -out user.crt`
  - For more information on this please read [here](https://eoyebami.github.io/k8s/2024-01-17-kubernetes-tls.html)
* However there is a way to automate this process by generating a `CertificateSigningRequest`
  - The admin would take the csr and base64 encode it
    * `cat user.csr | base64 | tr -d "\n"` # cats csr, encodes it, and deletes all newline characters in the output 
  - Then add this value into the `CertificateSigningRequest` yaml before applying it
    * YAML:
 
   ```yml
   cat <<EOF | kubectl apply -f - # applies piped content
   apiVersion: certificates.k8s.io/v1
   kind: CertificateSigningRequest
   metadata:
     name: user01
   spec:
     groups:
     - system:masters
     expirationSeconds: 600
     signerName: kubernetes.io/kube-apiserver-client # who will be signing the certificate
     usages:
     - client auth
     request:
       xxxxx # csr in base64
   EOF
   ```
  
  - See if you can view csr object and approve it
    * `kubetl get csr`
    * `kubectl certificate approve user01`
  - Retrieve the certificate and decode it for the user
    * `kubectl get csr user01 -o jsonpath='{.status.certificate}' | base64 -d > user01.crt`
  - You can create a role and bind it to the user
* NOTE: more info on this object can be found [here](https://kubernetes.io/docs/reference/access-authn-authz/certificate-signing-requests/#cluster-trust-bundles)
