<h1>GCP SSH</h1>
In GCP there are multiple ways to connect to a VM instance:
- Using Gcloud command
- Open in Browser Window using provided private SSH Key
- Open in Browser Window
- Open in Browser Window on custom port
- Using another ssh client
<h3>GCP SSH: gcloud</h3>
Rquirements to use gcloud:
- Google Cloud SDK must be installed
- Make sure your instance is created properly in its project
- Authenticate on your cli with the gcloud command (similar to aws configure)
  * `gcloud auth login` or `gcloud auth activate-service-account --key-file=<path_to_key_file>` if you are using a service account
- Use gcloud to ssh into instance
  * `gcloud compute ssh --zone <vm_zone> <vm_name> --tunnel-through-iap --project <project_id>`
  * By default gcloud uses something called `IAP (Identity-Aware Proxy) tunneling` to access vms
    - Otherwise you can use the external ip to ssh into the instance, but you'd need to whitelist specific ips for security purposes
<h5>What is IAP?</h5>   
