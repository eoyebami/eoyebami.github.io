<h1>IstioOperator</h1>
 
`IstioOperator` allows users to create configuration for istio control plane installation. Unlike the other installation methods, we can define a YAML file that defines the desired state of each Istio release

<h2>IstioOperator: Specifications</h2>
 
* Before going into the specifications, istio currently recognizes the apiVersion as `install.istio.io/v1apha1`
* `spec`:
- `profile`: which profile do you want to set, if not set `default` profile will be used
- `meshConfig`: config used by control plane components
  * `connecTimeout`: connection timeout for envoy
  * `ingressService`: name of k8 service used for istio ingress controller, if none is set will default to `istio-ingressgateway`
  * `enableTracing`: flag to control generation of trace spans and request
  * `accessLogFile`: file address for the proxy log (e.g. /dev/stdout), if empty access logs are disabled
  * `accessLogFormat`: format for the proxy access log, empty value results in envoy's default access log format
  * `accessLogEncoding`: encoding for proxy access logs, default is `TEXT` but `JSON` can be used
  * `defaultConfig`: 
    - `configPath`: path to the generated config file dir
    - `binaryPath`: path to proxy binary
    - `drainDuration`: time in seconds that envoy will drain connections during a hot restart (e.g. 1s/1m/1h)
    - `controlPlaneAuthPolicy`: `AuthenticationPolicy` istio components should use, default is `MUTUAL_TLS`
    - `concurrency`: number of worker threads to run, if unset this will auto determined based on cpu limits/request, if set to 0, will use all cores, default is 2 threads
    - `meshId`: unique identifier for service mesh
    - `readinessProbe`: VM health checking probe, mirrors kubernetes readiness probe
    - `holdApplicationUntilProxyStarts`: bool; adds hookds to delay application start up until pod proxy is ready to mitigate startuprace conditions
    - `caCertificatesPem`: PEM data of extra root certs for workload-to-workload communication
    - `image`: specifiies the details of the proxy image
    - More info [here](https://istio.io/latest/docs/reference/config/istio.mesh.v1alpha1/#ProxyConfig)
  * `outboundTrafficPolicy`: default behavior for sidecars handling outbound traffic
    - `mode`: mode, default is `ALLOW_ANY`
  * `inboundTrafficPolicy`: default behavior for sidecars handling inbound traffic
    - `mode`: either `PASSTHROUGH` for send traffic to pod IP or `LOCALHOST` for destinations listening on localhost
  * `enableAutoMtls`: enables mutual TLS automatically for service-to-service communication within a mesh, default is true
  * `dnsRefreshRate`: configures DNS refresh rate for envoy cluters of type `STRICT_DNS`, default is 60s
  * `meshMTLS`:
    - `minProtocolVersion`: minimum TLS protocl version to use, default is `TLSV1_2`
- `components`:
  * `base`: configuration for base components
  * `pilot`: configuration for internal components
  * `istiodRemote`: configurations for istiod component
  * `ingressGateways: configurations for ingressgateway components
  * `egressGateways: configuraitons for egressgateway components
  * More info [here](https://istio.io/latest/docs/reference/config/istio.operator.v1alpha1/#IstioComponentSetSpec)

  ```yaml
  apiVersion: install.istio.io/v1apha1
  kind: IstioOperator
  spec:
    profile: default
    meshConfig:
      accessLogFile: /dev/stdout
      accessLogEncoding: JSON
      accessLogFormat: |
        {
        "protocol": "%PROTOCOL%",
        "upstream_service_time": "%REQ(x-envoy-upstream-service-time)%",
        "upstream_local_address": "%UPSTREAM_LOCAL_ADDRESS%",
        "duration": "%DURATION%",
        "upstream_transport_failure_reason": "%UPSTREAM_TRANSPORT_FAILURE_REASON%",
        "route_name": "%ROUTE_NAME%",
        "downstream_local_address": "%DOWNSTREAM_LOCAL_ADDRESS%",
        "user_agent": "%REQ(USER-AGENT)%",
        "response_code": "%RESPONSE_CODE%",
        "response_flags": "%RESPONSE_FLAGS%",
        "start_time": "%START_TIME%",
        "method": "%REQ(:METHOD)%",
        "request_id": "%REQ(X-REQUEST-ID)%",
        "upstream_host": "%UPSTREAM_HOST%",
        "x_forwarded_for": "%REQ(X-FORWARDED-FOR)%",
        "client_ip": "%REQ(True-Client-Ip)%",
        "requested_server_name": "%REQUESTED_SERVER_NAME%",
        "bytes_received": "%BYTES_RECEIVED%",
        "bytes_sent": "%BYTES_SENT%",
        "upstream_cluster": "%UPSTREAM_CLUSTER%",
        "downstream_remote_address": "%DOWNSTREAM_REMOTE_ADDRESS%",
        "authority": "%REQ(:AUTHORITY)%",
        "path": "%REQ(X-ENVOY-ORIGINAL-PATH?:PATH)%",
        "response_code_details": "%RESPONSE_CODE_DETAILS%"
      }
      enableAutoMtls: true
      meshMTLS:
        minProtocolVersion: TLSV1_2
      outboundTrafficPolicy:
        mode: ALLOW_ANY
      inboundTrafficPolicy:
        mode: PASSTHROUGH
      defaultConfig:
        holdApplicationUntilProxyStarts: true
    components:
      base:
        enabled: true
        k8s:
          hpaSpec:
            minReplicas: 3
      istiodRemote:
      - name: istiod
        enabled: true
        label:
          app: istiod
        k8s:
          affinity:
            nodeAffinity: {}
            podAntiAffinity:
              requiredDuringSchedulingIgnoredDuringExecution:
              - labelSelector:
                  matchLabels:
                    app: istiod
                topologyKey: kubernetes.io/hostname
      ingressGateways:
      - name: istio-ingressgateway
        enabled: true
        label: # labels for istiod
          app: istio-ingressgateway
        k8s:
          affinity:
            nodeAffinity: {}
            podAntiAffinity:
              requiredDuringSchedulingIgnoredDuringExecution:
              - labelSelector:
                  matchLabels:
                    app: istio-ingressgateway
                topologyKey: kubernetes.io/hostname
          service:
            ports:
              name: https-port
              protocol: HTTPS
              port: 443
              targetPort: 443
              nodePort: 31084
            externalTrafficPolicy: "Local"
            # with cluster, traffic is routed to any node in the cluster, regardless of whether node has a running pod for the service, traffic will then be forwarded to node that has pod running upon failure, this can cause client's ip to be masked
            # with local, traffic is only routed to nodes that have running pods for that service, this preserves clients source IP but can lead to uneven load distribution if a node has more active connections
          hpaSpec:
            minReplicas: 3
      egressGateways:
      - name: istio-egressgateways
        enabled: false
  ```

* Once your YAML has been completed, you may now install using `istioctl`
  
  ```
  # Deleting mutating webhooks to avoid overlapping of newer resources
  kubectl delete mutatingwebhookconfiguration istio-sidecar-injector
  kubectl delete mutatingwebhookconfiguration istio-revision-tag-default
  
  # Install YAML
  istioctl upgrade -f istio-custom.yaml -y
  kubectl rollout status deploy -nistio-sytem
  ```

