<h1>Istio Destination Rules</h1>
 
* A `DestinationRule` defines policies and configurations that are applied to traffic destined for a specific service
  - If we have a microservice that sits behind a kubernetes service, we define `DestinationRule` for how traffic will be routed to that service through the `envoy` proxy
* Before going over `Destination Rules` there are a couple specs that need to be defined first

<h2>Subsets</h2>
 
* `Subsets` are a way of defining groups of services to route traffic to
  - By Using labels we can split services into these groups, which can then be weighted for balancing in [Virtual Services](https://eoyebami.github.io/k8s/istio/2024-04-26-istio-virtual-services.html)

<h2>Traffic Polcies</h2>
 
* `Trafffic Policies` are applied for specific destionations across all destination ports
* Types:
  - `loadBalancer`: define loadbalancing policies
    * `simple`: set loadbalancing policy like `ROUND_ROBIN, LEAST_CONN, RANDOM, PASSTHROUGH`
  - `connectionPool`: `Circut Breaking`; controls volume of connections to an upstream service
    * `tcp`: define settings for [tcp](https://istio.io/latest/docs/reference/config/networking/destination-rule/#ConnectionPoolSettings-TCPSettings)
    * `http`: define settings for [http](https://istio.io/latest/docs/reference/config/networking/destination-rule/#ConnectionPoolSettings-HTTPSettings)
 
    ```yml    
    spec:
      host: test.default.svc.cluster.local
      trafficPolicy:
        connectionPool:
          tcp:
            maxConnections: 3 # limiting the amount of concurrent connections to this svc
            connectionTimeout: 10s # timeout for each connection
            maxConnectionDuration: 60s # max time each connection can sustain itself
          http:
            maxConcurrentStreams: 3 # limiting the amount of concurrent connections
            maxRequestsPerConnection: 10 # limiting requests each connection can make
            http1MaxPendingRequests: 5 # max number of requests can be queued for this service
            idleTimeout: 10s # idle timeout for connections
    ```    

  - `outlierDetection`: controls eviction of unhealthy hosts from load balancing pool
    * `consecutive5xxErrors`: number of 5xx errors from before host is ejection from pool
    * `interval`: interval between ejection sweep analysis
    * `consecutiveGatewayErrors`: number of gw errors before host is ejected from pol
    * More info [here](https://istio.io/latest/docs/reference/config/networking/destination-rule/#OutlierDetection)
  - `tls`: SSL/TLS related settings for upstream connections, to proxy sidecars of the application services
    * `mode`: define settings for [mode](https://istio.io/latest/docs/reference/config/networking/destination-rule/#ClientTLSSettings-TLSmode)
    * `insecureSkipVerify`: skips verification if set to `true`
    * More info [here](https://istio.io/latest/docs/reference/config/networking/destination-rule/#ClientTLSSettings)
  - `portLevelSettings`: configure traffic policies for individual ports
    * `port`: port this policy will apply to
    * `loadbalancer`: setting lb settings for this specific port
    * `connectionPool`: controls volume of connections to an upstream service at specified port
    * `outlierDetection`: controls eviction of unhealthy hosts from load balancing pool at that port
    * `tls`: tls configs at this port
    * More info [here](https://istio.io/latest/docs/reference/config/networking/destination-rule/#TrafficPolicy-PortTrafficPolicy)
  - `tunnel`: config for tunneling TCP over transport or application laters for host configured in dr
    * `protocol`: which protocol to use for tunneling the downstream connection
    * `targetHost`: specifies a host to which the downstream connection is tunneled, must be FQDN or IP 
    * `targetPort`: specifies a port to which connection is tunneled
    * More info [here](https://istio.io/latest/docs/reference/config/networking/destination-rule/#TrafficPolicy-TunnelSettings)
  - `proxyProtocol`: upstream PROXY protocol settings
    * `version`: proxy protocol version to use
    * More info [here](https://istio.io/latest/docs/reference/config/networking/destination-rule/#TrafficPolicy-ProxyProtocol)

* More info on configuring `Traffic Policies` can be found [here](https://istio.io/latest/docs/reference/config/networking/destination-rule/#TrafficPolicy)
    
  ```yml
  apiVersion: networking.istio.io/v1aplha3
  kind: DestinationRule
  metadata:
    name: istio-destination-rule
  spec:
    host: dataengine.default.svc.cluster.local
    trafficPolicy:
      loadBalancer: # by default envoy uses a roundrobin policy to balance traffic
        simple: ROUND_ROBIN # setting a traffic policy of loadbalancer allows you to change this configuration
      tls:
        mode: ISTIO_MUTUAL # uses istio cert for verification
    subsets: # subsets can be used for A/B Testing (testing 2 versions of an application, one usually being available to a small sample size)
    - name: v1
      labels:
        versions: v1 # label on deployments that will be associated with this subset
      trafficPolicy:
        loadBalancer:
          simple: RANDOM # traffic policies can also be defined at the subset level too
  ```

