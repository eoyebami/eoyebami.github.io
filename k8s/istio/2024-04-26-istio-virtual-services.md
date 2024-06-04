<h1>Istio Virtual Services</h1>
* The `Istio Gateways` and the `Istio Gateway Controllers (ingress & egress)` have now been set up
  - Traffic can now route in and out of the cluster through these gateways, but now we'll need to configure which services the traffic will be directed to
* `Virtual Services` define a set of routing rules coming from the `ingressgateway`

<h2>Virtual Services: Configurations</h2>
* In the specifications of the `Virtual Service` there are multiple components to take note of
* `gateways`: names of gateways that should apply the defined routes
* `hosts`: must be matching to one of the hosts define in the gateway's server specifications

<h4>HTTP Routing</h4>
* `http`: ordered lists of route rules for HTTP traffic
  - `name`: name of rule
  - `match`: 
    * `name`: name of match
    * `uri`: uri of match values, can be `exact, prefix, or regex`
    * `scheme`: uri scheme
    * `method`: http method
    * `authority`: http authority
    * [headers](https://istio.io/latest/docs/reference/config/networking/virtual-service/#Headers): add or remove header in request or response
    * `port`: specifies port on port being accessed
    * `queryParams`: query parameters for matching 
    * More info [here](https://istio.io/latest/docs/reference/config/networking/virtual-service/#HTTPMatchRequest)
  - `route`: list of destinations to route traffic to
    * More info [here](https://istio.io/latest/docs/reference/config/networking/virtual-service/#HTTPRouteDestination)
  - `redirect`: http rule to redirect traffic
    * `uri`: uri that will overwrite all matched http uris
    * `authority`: host that will overwrite host of matched http uris
    * `port`: post that will overwrite host of matched http uris
    * `derivePort`: dynamically set port, can be `FROM_PROTOCOL_DEFAULT` which is either 80 or 443 or `FROM_REQUEST_PORT` to use request port
    * More info [here](https://istio.io/latest/docs/reference/config/networking/virtual-service/#HTTPRedirect)
 
    ```yml
    # example redirect
    http:
    - match:
      - uri:
        exact: /path
      redirect:
        uri: /test
        authority: test.default.svc.cluster.local
        derivePort: FROM_PROTOCOL_DEFAULT
    ```

  - `directResponse`: used to specify a fixed response that should be sent for match uris
    * `status`: status code
    * `body`: insert a string or bytes with message
    * More info [here](https://istio.io/latest/docs/reference/config/networking/virtual-service/#HTTPDirectResponse)
 
    ```yml
    # example with regular string
    http:
    - match:
      - uri:
        exact: /v1/getProductRatings
      directResponse:
        status: 503
        body:
          string: "unknown error"

    # example with strings with json
    http:
    - match:
      - uri:
        exact: /v1/getProductRatings
      directResponse:
        status: 503
        body:
          string: "{\"error\": \"unknown error\"}" # setting string in json
      headers:
        response: # setting response header to json
          set:
            content-type: "application/json"
  
    # example wth bytes
    http:
    - match:
      - uri:
        exact: /v1/getProductRatings
      directResponse:
        status: 503
        body:
          bytes: "dW5rbm93biBlcnJvcg==" # "unknown error" in base64
    ```

  - `delegate`: specifies the VS which can be used to define delegate HTTPRoutes
    * `name`: name of delegate VS
    * `namespace`: namespace of delegate VS
    
    ```yml
    http:
    - match:
      - uri:
        prefix: "/productpage"
      delegate: # forwards matched uri to defined delegate virtual service
        name: productpage
        namespace: nsA
      # routes can't be defined for virtual services that have delegates defined
    ```

  - `rewrite`: rewrites matched uris
    * `uri`: new uri
    * More info [here](https://istio.io/latest/docs/reference/config/networking/virtual-service/#HTTPRewrite)
  - `timeouts`: timeout for HTTP requests 
     
    ```yml
    # configured in a virtual service
    route:
    - destination:
        host: test.default.svc.cluster.local
        port:
          number: 8080
      timeout: 5s # connection timeout at 5 seconds
    ```

  - `retries`: retry policy for HTTP requests
    * `attempts`: number of retries
    * `perTryTimeout`: delay per retry
    * `retryOn`: what should trigger a retry, policies can be found [here](https://www.envoyproxy.io/docs/envoy/latest/configuration/http/http_filters/router_filter#x-envoy-retry-on)
    * More info [here](https://istio.io/latest/docs/reference/config/networking/virtual-service/#HTTPRetry)
   
    ```yml
    # fault delay
    http:
    - retries:
        attempts: 3 # istio default is 2 retries
        perTryTimeout: 2s # istio default is 25ms intervals per timeout
      route:
      ...
      ```

  - `fault`: FAULT INJECTION used to emulate failures
    * `delay`: delay requests forwarding
    * `abort`: apport requests
    * Note: these 2 fields work independently of one another
    * Note: timeouts and retries cannot function when faults are enabled
 
    ```yml
    # fault delay
    http:
    - fault:
        delay:
          fixedDelay: 5s
          percentage: 
            value: 0.1 # 10% of endusers will experience a 5s delay
      route: 
      ...

    # fault abort
    http:
    - fault:
        abort:
          httpStatus: 400
          percentage:
            value: 0.1 # 10% of endusers will experience a 400 error
      route:
      ...
    ```

  - `mirror`: mirror HTTP trafic to another destination
    * Note: more info [here](https://istio.io/latest/docs/reference/config/networking/virtual-service/#HTTPMirrorPolicy)
  - `corsPolicy`: cross-origin resource sharing policy, defining what hosts, methods, and headers can be sent in a request to access this service
    * `allowOrigins`: origin url that is allowed, `exact, prefix, regex`
    * `allowMethods`: list of HTTP methods allowed to access resource
    * `allowHeaders`: list of HTTP headers that can be used when requesting the resource
    * `exposeHeaders`: list of HTTP headers the browsers are allowed to access
    * `maxAge`: how long preflight request can be cached
    * `allowCredentials` defines whether or not origin can send request using credentials (such as cookies, HTTP authentication, and client-side SSL certs)
    * More info [here](https://istio.io/latest/docs/reference/config/networking/virtual-service/#CorsPolicy)
 
    ```yml
    # example cors policy
    http:
    - route:
      - destination:
          host: ratings.prod.svc.cluster.local
          subset: v1
      corsPolicy:
        allowOrigins:
        - exact: https://example.com
        allowMethods:
        - POST
        - GET
        allowCredentials: false
        allowHeaders:
        - X-Foo-Barwha
        maxAge: "24h"
    ```
 
  - [headers](https://istio.io/latest/docs/reference/config/networking/virtual-service/#Headers): add or remove header in request or response
    * The difference between manipulating headers at the http level rather than the route is, at the http level it will hit all matched uris, at the route level you can define headers for specific destinations

 ```yml
  apiVersion: networking.istio.io/v1alpha3
  kind: VirtualService
  metadata:
    name: istio-virtual-service
  spec:
    gateways: # associate this virtual service with specified gateway
    - istio-gateway
    hosts: # defines hostname to which this virtual service applies
    - "dataengine.com"
    http:
    - match: # specifies uris that should be matched
      - uri:
          exact: /path # matches the exact path
      - uri:
          prefix: /path # matches path that has this prefix
      headers: # setting headers at HTTP level
        request:
          set:
            test: "true"
      retries:
        attempts: 3 # how many times to retry
        perTryTimeout: 60s # initialDelay per retry
        retryOn: connect-failure,refused-stream
      route:
      - destination: # all traffic matching the uri specified in http spec will be routed to the destination defined in route
          host: dataengine.default.svc.cluster.local # service that sits in front of dataengine microservice
          subset: v1 # subset defined in destination rule
          port:
            number: 80
          headers: # remove response headers from this specific destination route
            response:
              remove:
              - foo
        weight: 100 # what percentage of traffic should be directed to this destination, use in case of multiple destinations
  ```

<h4>TLS Routing</h4>
* `tls`: ordered list for HTTPS/TLS traffic, forwards unterminated TLS traffic, if tls mode in the gw is set to `PASSTHROUGH`
  - `match`:
    * `sniHosts`: server name indicator to match on, similar to a 
    * `port`: port host is being addressed on
    * More info [here](https://istio.io/latest/docs/reference/config/networking/virtual-service/#TLSMatchAttributes)
  - `route`:
    * More info [here](https://istio.io/latest/docs/reference/config/networking/virtual-service/#HTTPRouteDestination)
   
  ```yml
  # matching snihosts for tls connections
  tls:
  - match:
    - port: 443
      sniHosts:
      - dataengine.com
    route:
    - destination:
        host: dataengine.default.svc.cluster.local
  ```

<h4>TCP Routing</h4>
* `tcp`: ordered list for TCP traffic
  - `match` :
    * `port`: matches port on host being addressed
  - `route`:
    * More info [here](https://istio.io/latest/docs/reference/config/networking/virtual-service/#HTTPRouteDestination)

  ```yml
  tcp:
  - match:
    - port: 8888
    route:
    - destination:
      host: dataengine.default.svc.cluster.local
      port:
        number: 8080
  ``` 

<h4>ExportTo</h4>
* Allows you to export a virtual service to a list of namespaces to be used by sidecars and gateways in those namespaces
  - `.`: defines present working namespace
  - `*`: defines all namespaces

* More info on `Destination Rules` can be found [here](https://eoyebami.github.io/k8s/istio/2024-04-26-istio-destination-rules.html)
