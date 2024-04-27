<h1>Istio Traffic Management</h1>
* Now that we have all of our istio components installed, we'll go over how we can route our end users through our service mesh to access our services

<h2>Istio Gateways</h2>
* `Istio Gateways` are loadbalancers that sit at the each of the mesh, that manage both inbound and outbound traffic to your services
 - They function pretty similarly to how ingress works in kubernetes
 - You can route traffic to specific services based on rules defined in ingress
 - `Gateways` function in the same way, although `istio` has a lot more features
* When `istio` is deployed into the system, 2 components may run in that `istio-system` namespace depending on how you configured `istio`
  - They are known as `Istio Gateway Controllers`:
    * `istio-ingressgateway`: manages all inbound traffic to your services
    * `istio-egressgateeway`: manages all outfound traffic from your services
  - These controllers are simply standalone envoy proxies, the same which run as sidecars in our microservices pods
* You can create a gateway and configure how it should manage traffic at the hosts you specified
  - A gateway would need to be defined for both egress and ingress traffic

  ```yml
  apiVersion: networking.istio.io/v1alpha3
  kind: Gateway
  metadata:
    name: istio-gateway
  spec:
    selector: # select for the ingress gateway controller you want to balance traffic through
      istio: ingressgateway # default label set for all ingressgateways
    servers:
    - port: # any traffic coming in from the host at this port will be directed to the selected ingressgateway 
        number: 80
        name: http
        protocol: HTTP
      hosts: # multiple hosts can be specified
      - "dataengine.com"
      tls:
        httpsRedirect: true # any traffic coming in at port HTTP will be redirected to HTTPS
    - port: # enable HTTPS
        number: 443
        name: https
        protocol: HTTPS
      hosts:
      - "dataengine.com"
      tls: # will fill out later
  ---
  apiVersion: networking.istio.io/v1alpha3
  kind: Gateway
  metadata:
    name: istio-gateway
  spec:
    selector: # select for the ingress gateway controller you want to balance traffic through
      istio: egressgateway # default label set for all egressgateways
    servers:
    - port: # enable HTTPS
        number: 443
        name: 
        protocol: 
      hosts:
      - "dataengine.com"
      tls: # will fill out later
  ```

