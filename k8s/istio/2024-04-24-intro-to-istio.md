<h1>Introduction to Istio Service Mesh</h1>
 
* What is a service mesh?
  - A service mesh is a dedicated infrastructure layer designed to manage, observe, and control communication between microservices within a cluster
  - Using works by installing proxies as sidecars along side the microservices
    * The proxies communicate with each other through a data plane and the communicate to a server-side component known as a controlplane
    * Think of it as how `kube-proxy`, a data plane component, communicates with `kube-apiserver`, a control plane component, to create appropriate rules on each node to initiate traffic management for `Services`
    * Here in a service mesh, the control plane manages all traffic to and from services through the proxy sidecars
* `Traffic Management`: with a service mesh you can control how services talk to each other 
* `Security`: when communicate does occur between microservices, mTLS will be enabled to secure your workloads. 
* `Observability`: you'll also have increased observability in your services to debug and troubleshoot issues occurring between microservices
* `Service Discovery`: is also another aspect of a service mesh, not only does it discover what service is running at which port, but it also routes traffic via health checks, to services that are up (making sure end users don't get routed to services with failed checks).

<h2>What is Istio</h2>
 
* `Istio` is an open source service mesh that provides a way to secure, connect, and monitor microservices 
* Istio Components:
  - `Control Plane`: `Istios` control plane consists of 3 services that were later combined to form the service known as `istiod`:
    * `Citadel`: manages certificate generation
    * `Pilot`: helps with service discovery
    * `Galley`: validates configuration files
  - `Data Plane`: `Istios` data plane consists of:
    * `Envoy`: the proxy `istio` uses in its data plane to manage traffic across services
    * `Istio-Agent`: responisble for passing configuration secrets to the `envoy` proxies
