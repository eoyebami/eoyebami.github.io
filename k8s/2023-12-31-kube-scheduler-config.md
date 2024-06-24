<h1>Kube-Scheduler Configuration</h1>
 
<h2>Extension Points</h2>
 
* There are multiple steps a pod must go through before being scheduled on a node:
  - `scheduling queue`: which defines the order in which the pods are scheduled (this can be affected by a `PriorityClass` object)
    * Plugins: `PrioritySort`: sorting pods by priority
  - `filter`: filtering out nodes that do not meet the requirements
    * Plugins: `NodeResourceFit, NodeName, NodeSchedulable`: filters out nodes that don't meet resource requirements or are unschedulable
  - `scoring`: scheduler assigns a score to the nodes with different weights
    * Plugins: `NodeResourceFit, ImageLocality`: assigns higher score to nodes with enough resources or with the image already pulled on the node
  - `binding`: pod is found to a node with the highest score
    * Plugins: `DefaultBinder`: facilitates the pod binding to the node

* Kubernetes allows us to configure which plugins go to which stage by defining these stages as `Extension Points`, and assigning plugins to those points
  - `queueSort`
  - `filer`
  - `score`
  - `bind`

  * NOTE: these are just the main `Extension Points`, there are many more that can be found [here](https://kubernetes.io/docs/reference/scheduling/config/#extension-points)
<h2>Profiles</h2>
 
* With Profiles, you can have one `Kube-Scheduler` behave as multiple by using the `KubeSchedulerConfiguration`
  - You can define plugins you'd like disabled/enabled for an `Extension Point` for each profile

* Ex:

```yml
apiVersion: kubescheduler.config.k8.io/v1
kind: KubeSchedulerConfiguration
profiles:
- schedulerName: my-scheduler-1
  plugins:
    score:
      disabled:
      - name: TaintToleration
      enabled:
      - name: MyCustomPluginA
- schedulerName: my-scheduler-2
  plugins:
    preScore:
      disabled:
      - name: "*" (disables all plugins at this point)
    score:
      disabled:
      - name: "*"
```

* With this you can now specify a profile you'd like to be in charge of scheduling a pod

```yml
spec:
  schedulerName: my-scheduler
  containers:
  - name: nginx
    image: nginx
```

<h2>Multiple Kube-Schedulers</h2>
 
* You can configure you own scheduler to run in a kubernetes cluster by either deploying the binary yourself into a master node, or by deploying as a pod using the `KubeAdm` method
* Using a `KubeSchedulerConfiguration` object you can define the configuration of the scheduler, which you can either pass as a file in the `--config` configuration, or in the case of it running as a pod, you can pass it as a `ConfigMap` which you can mount into the pod
  - You can use this object to:
    * disable/enable specific plugins for a scheduler's extension points (stages scheduler goes through for assigning pods; which have plugins that affect their functionality; more info [here](https://kubernetes.io/docs/reference/scheduling/config/))
    * set weights for plugins with greater priority

* Ex:

   ```yml
   apiVersion: kubescheduler.config.k8.io/v1
   kind: KubeSchedulerConfiguration
   profiles:
   - schedulerName: my-scheduler
   leaderElection:
     leaderElect: true (selecting with scheduler will be the leader in a cluster)
     resourceNamespace: kube-system
     resourceName: lock-object-my-scheduler
   ```

* The main point in all this is you can specify which scheduler you want managing the scheduling of a pod 
  - Ex:

   ```yml
   spec:
     schedulerName: my-scheduler
     containers:
     - name: nginx
       image: nginx
   ```

* To see which scheduler scheduled a pod, you can check the events or view the logs of the `kube-scheduler` pod
  - `kubectl get events -o wide`
  - `kubectl logs -f <pod-name> -n kube-system`
