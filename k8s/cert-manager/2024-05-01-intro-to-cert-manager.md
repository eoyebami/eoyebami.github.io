<h1>Introduction to Cert Manager</h1>
 
* `Cert-Manager` is a tool that automates the management and issuance of TLS certificates
* `Cert-Manager` fulfills a variety of different tasks:
  - `Certificate Issuance`: `Cert-Manager` integrates with numerous CAs (listed below) to automate the process of obtaining certs
    * `Lets Encrypt`
    * `HashiCorp Vault`
    * `CloudFlare`
    * `Self-Signed`
    * More info on issuers can be found [here](https://cert-manager.io/docs/configuration/issuers/)
  - `Certificate Lifecycle Management`: `Cert-Manager` automaes the lifecycle management of the certs through automated renewal and recovation, by monitoring the expiration of the certs and renewing them prior to

<h2>Installing Cert Manager</h2>
 
* There are a variety of ways to install `Cert-Manager` on a cluster, of of which can be found [here](https://cert-manager.io/docs/installation/)
* The method I will be using is `kubectl`
  - `kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.14.5/cert-manager.yaml`: we can use this to install the `Cert-Manager` release manifests into our cluster
    * This will create a namespace called `cert-manager` and install all necessary resources for cert-manager to function
