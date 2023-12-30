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
  - `kubectl run nginx --image=nginx --namespace=default --labels tier=front-end --port=8080`: creates an `nginx pod` in default ns, with labels and a port
  - `kubectl run nginx --image=nginx --dry-run=client -o yaml`: generates the pod manifest yaml file
    * `--dry-run=client`:  previews object that would be sent to cluster, without creating it
<h4>Deployments</h4>
  - `kubectl create deployment --image=nginx nginx --replicas=4 --namespace=default --port=8080`: creates a deployment with the name `nginx` and sets the `replicaset` to 4
  - `kubectl create deployment --image=nginx nginix --replicas=4 --dry-run=client -o yaml > nginx-deployment.yaml`: creates a deployment with the name `nginx` image, with the name `nginx`, with a `replicaset` set tot 4, then previews the object creation
    * The `-o yaml` outputs the yaml and its all redirected into a yaml file
  - `kubectl set image deployment nginx nginx=nginx:1.18`: will set a new image to the deployment
<h4>ReplicaSets</h4>
  - `kubectl scale rs --replicas=n`: scales up a `replicaset` to the value desired
<h4>Services</h4>
  - `kubectl expose deployment myapp-deployment --type=ClusterIP --name=myapp-service --port=80 --target-port=8080 --namespace=default  --name=myapp-svc`: creates a `ClusterIP` svc exposed to the deployment myapp-deployment, the `targetPort` is `8080` and the svc `port` is 80
  - `kubectl expose pod myapp-deployment --type=ClusterIP --name=myapp-service --port=80 --target-port=8080 --namespace=default --name=myapp-svc`: creates a `ClusterIP` svc exposed to the deployment myapp-deployment, the `targetPort` is `8080` and the svc `port` is 80
  * Ex:
    - `kubectl expose deployment myapp-deployment --type=LoadBalancer --name=myapp-svc --target-port=80 --port=80`
    - `kubectl create svc nodeport nginx --tcp=80:80--node-port=30000`: the only way to create a svc and specify the NodePort; creates a nodeport svc named nginx
<h4>Namespaces</h4>
  - `kubectl create ns dev`: creates ns named dev
<h4>Filtering Labels</h4>
  - `kubectl get <object> --selector app=webapp --selector tier=front-end`: will for objects with match the labels in the selectors flag
    * NOTE: If you are looking to run a `wc -l` to count the pods, use `--no-headers` to remove the headers when retrieving the data.
  - `kubectl get <object> <resource_name> -o json`
    * outputs the resource data in json format, which you can use `jq` to filter through
  - `kubectl get <object> <resource_name> -o jsonpath='{.metadata.labels}'`
    * outputs the resource in json, then parses through the data, in this case, for the labels of the resource
    * you can filter for anything in the json, as long as you know what you're looking for    
