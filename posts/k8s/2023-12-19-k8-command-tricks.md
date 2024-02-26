<h2>Kubectl Commands</h2>
* In `kubectl`, we have imperative and declarative commands
  - imperative: you provide exact instructions on how something should be created in k8. May fail when trying to create something that already exists, or replace something that doesn't exist
    * `kubectl run`
    * `kubectl create`
    * `kubectl replace`
  - declarative: you give specifications and allow k8 to figure out the rest. Knows to replace if the object already exists, and to create if it doesn't.
    * `k apply`: can be applied on individual yaml files, or against directories

* If you dont want to have to write out the yaml file, heres a couple `kubectl` cmds to remember for easy resource creation
<h4>Pods</h4>
* CMDs for Pod manipulation 
  - `kubectl run nginx --image=nginx --namespace=default --labels tier=front-end --port=8080`: creates an `nginx pod` in default ns, with labels and a port
  - `kubectl run nginx --image=nginx --dry-run=client -o yaml`: generates the pod manifest yaml file
    * `--dry-run=client`:  previews object that would be sent to cluster, without creating it

<h4>Deployments</h4>
* CMDs for Deployment manipulation 
  - `kubectl create deployment --image=nginx nginx --replicas=4 --namespace=default --port=8080`: creates a deployment with the name `nginx` and sets the `replicaset` to 4
  - `kubectl create deployment --image=nginx nginix --replicas=4 --dry-run=client -o yaml > nginx-deployment.yaml`: creates a deployment with the name `nginx` image, with the name `nginx`, with a `replicaset` set tot 4, then previews the object creation
    * The `-o yaml` outputs the yaml and its all redirected into a yaml file
  - `kubectl set image deployment nginx nginx=nginx:1.18`: will set a new image to the deployment
  - `kubectl rollout history deploy <deployment-name>`: shows history of revisions made to that deployment
  - `kubectl rollout undo deploy <deployment-name>`: allows you to rollback a deployment revision to a previous one

<h4>ReplicaSets</h4>
* CMDs for RS manipulation 
  - `kubectl scale rs --replicas=n`: scales up a `replicaset` to the value desired

<h4>Services</h4>
* CMDs for Svc manipulation 
  - `kubectl expose deployment myapp-deployment --type=ClusterIP --name=myapp-service --port=80 --target-port=8080 --namespace=default  --name=myapp-svc`: creates a `ClusterIP` svc exposed to the deployment myapp-deployment, the `targetPort` is `8080` and the svc `port` is 80
  - `kubectl expose pod myapp-deployment --type=ClusterIP --name=myapp-service --port=80 --target-port=8080 --namespace=default --name=myapp-svc`: creates a `ClusterIP` svc exposed to the deployment myapp-deployment, the `targetPort` is `8080` and the svc `port` is 80
  * Ex:
    - `kubectl expose deployment myapp-deployment --type=LoadBalancer --name=myapp-svc --target-port=80 --port=80`
    - `kubectl create svc nodeport nginx --tcp=80:80--node-port=30000`: the only way to create a svc and specify the NodePort; creates a nodeport svc named nginx

<h4>ConfigMaps</h4>
* CMDs for Cm manipulation 
  - `kubectl create configmap <cm-name> --from-literal=<key-name>=<value>  --from-literal=key-name-2=value-2`: provide the key/value pairs directly in the command
  - `kubectl create configmap <cm-name> --from-file=<key-name>=app-config.properties`: provide the key/value pairs from a properties file as a multi-lined string

<h4>Secrets</h4>
* CMDs for Secret manipulation 
  - `kubectl create secret generic <secret-name> --from-literal=<key-name>=<value>`: used to create individual key/value pairs in the object
  - `kubectl create secret generic <secret-name> --from-file=<key-name>=app-config.properties`: used to add the contents of the file in a key as a multi-lined string

<h4>Namespaces</h4>
* CMDs for Ns manipulation 
  - `kubectl create ns dev`: creates ns named dev

<h4>Filtering Labels</h4>
* CMDs for Filter manipulation 
  - `kubectl get <object> --selector app=webapp --selector tier=front-end`: will for objects with match the labels in the selectors flag
    * NOTE: If you are looking to run a `wc -l` to count the pods, use `--no-headers` to remove the headers when retrieving the data.
  - `kubectl get <object> <resource_name> -o json`
    * outputs the resource data in json format, which you can use `jq` to filter through
  - `kubectl get <object> <resource_name> -o jsonpath='{.metadata.labels}'`
    * outputs the resource in json, then parses through the data, in this case, for the labels of the resource
    * you can filter for anything in the json, as long as you know what you're looking for    
  - `kubectl get deploy <name> -o jsonpath='{.spec.template.spec.containers[*].name}'`
  - `kubectl get pod <name> -o jsonpath='{.spec.containers[*].name}'`
    * outputs the name of the containers running in this pod
  - `kubectl get deploy <name> -o jsonpath='{.spec.template.spec.containers[*].image}'`
  - `kubectl get pod <name> -o jsonpath='{.spec.containers[*].image}'`
    * outputs the images of the containers running in this pod
  - `kubectl get pods webapp-2 -o jsonpath='{range .spec.containers[*]}{.name} {.image}{"\n"}'`
    * outputs both image and name
  - `kubectl get pods <pod-name> -o jsonpath='{.status.containerStatuses}'`: displays status of containers
  - `kubectl get pods <pod-name> -o jsonpath='{.status.containerStatuses[].state}'`: displays state of containers
  - `kubectl get pods <pod-name> -o jsonpath='{.status.initcontainerStatuses}'`: displays status of initcontainers
  - `kubectl get pods <pod-name> -o jsonpath='{.status.initcontainerStatuses[].state}'`: displays state of initcontainers
  - `kubectl get csr user01 -o jsonpath='{.status.certificate}' | base64 -d > user01.crt`: retrieves certificate signed by cluster and outputs to a crt file
  - `<stdin> | kubectl apply -f -`: will apply whatever input it receives into the cluster

<h4>KubeConfig</h4>
* CMDs for Kubeconfig manipulation 
  - `kubectl config view`: displays kubeconfig data
    * you can specify a kubeconfig by using `--kubeconfig=<fileName>`
  - `kubectl config use-context <context-name>`: change context, otherwise known as switching between cluster/user pairs
  - `kubectl config set-context --current --namespace <ns>`: setting a namespace for the current context

<h4>Authorization</h4>
* CMDs for Auth manipulation 
  - `kubectl auth can-i <verb> <object>`: will return `yes` or `no` depending on your authorization level
    * verbs can include: `get, create, patch, update, delete, list, watch`
  - `kubectl auth can-i <verb> <object> --as <user>`: will check if a user you specified can run that verb on that object
  - `kubectl auth can-i <verb> <object> --as-group <user> --as <random-string>`: will check if a group you specified can run that verb on that object
    * random string is used impersonate a user who would be in that group, command fails without a username for `kube-apiserver` to identify the group with
  - `kubectl get <object> <object-name> --as <user>`: will allow you to test any kubectl command as that user
  - `kubectl get <object> <object-name> --as-group <user> --as <random-string>`: will allow you to test any kubectl command as a user in that group
    * random string is used impersonate a user who would be in that group, command fails without a username for `kube-apiserver` to identify the group with
  - `kubectl api-resources --namespaced=true`: lists all namespaced resources
  - `kubectl api-resources --namespaced=false`: lists all non-namespaced resources

<h4>ServiceAccounts</h4>
* CMDs for Serviceaccount manipulation 
  - `kubectl create serviceaccount <name>`: creates service account
  - `kubectl create token <service-account-name> --duration <can be in sec(s) or hrs(h)>`: creates a token for a service account with a set duration

<h4>Logs</h4>
* CMDs for Log manipulation 
  - `kubectl logs -f <pod>`
    * outputs the logs of the first container that was defined in the yaml file
  - `kubectl logs -f <pod> <container-name>`
    * outputs the logs of the specified container in that pod

<h4>Commands for Debugging</h4>
* CMDs for Debugging and Troubleshooting 
  - `ip a` or `ip address`: displays information about IP addresses assigned to interfaces
    * `ip address show <eth-name>`: will display information about a specific interface
    * `ip address show type bridge`: will display information about bridge type interfaces
  - `ip link`: displays and manipulates network interfaces
    * gives info on whether they are up or down, gives MAC addresses as well
  - `netstat -nplt`: gives all ports that are being listened on the host and by what pid/program
    * `-n`: does not resolve the names, leave it as ip
    * `-p`: displays pid/program
    * `-l`: displays all listenting sockets
    * `-t`: for tcp sockets
