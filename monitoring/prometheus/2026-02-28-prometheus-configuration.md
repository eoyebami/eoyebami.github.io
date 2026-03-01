## Prometheus Configuration
- [Prometheus Configs](#prometheus-configs)
  - [Authentication](#authentication-and-encryption)
    - [Authentication](#authentication)
    - [Encryption](#encryption)
  - [Changes to Configs](#changes-to-prometheus-configs)

### Prometheus Configs

* Once we have the `node_exporters` collecting metrics from the nodes. This can be done in the `prometheus.yml`

    ```yaml
    global: # default params for all other config sections
      scrape_interval: 1m
      scrape_timeout: 10s

    # define targets and configs for metrics collection
    scrape_configs:
        - job_name: 'node' # a job is a collection of instances that need to be scraped
          # configs for scraping nodes in a job, take precedence over global configs
          scrape_interval: 30s # metrics will be fetched every 30s
          scrape_timeout: 5s # if request takes longer than 5 seconds, the scrap will timeout
          sample_limit: 1000
          scheme: https # protocol to use when scraping metrics, default is HTTP
          metrics_path: /metrics # default path, but you can set you're own path in the node_exporter
          basic_auth: # authentication for node_exporter
            username: <string>
            password: <string>
            password_file: <string>
          static_configs: 
            - targets: ['10.0.0.1:9100'] # set of targets to scrape
              labels:
                node: "node01" # labels node in targets
            - targets: ['10.0.0.2.:9100'] # multiple target arrays can bet set
              labels:
                node; "node02"

    alerting: # configs for AlertManager

    rule_files: # rule files specify a list of files rules are read from

    # settings related to the remote read/write feature
    remote_read:
    remote_write:

    storage: # storage related settings
    ```

- #### Authentication and Encryption

    ##### Authentication

    * By default there is no authentication between `prometheus` and the `node_exporter` that it is pulling the metrics from. Meaning anyone with access to the exporter can also pull your metrics as well. 
    * When generating Authentication for prometheus-to-exporter, you'll need to first generate a hash of your password

        ```console
        # either use python or instal apache2-utils
        sudo apt install apache2-utils
        htpasswd -nBC 12 " " | tr -d ':\n' # it will prompt you for a password
        ```

        ```yml
        # update config.yml for node_exporter
        tls_server_config:
          cert_file: /etc/node_exporter/node_exporter.crt
          key_file: /etc/node_exporter/node_exporter.key
        basic_auth_users:
          prometheus: <hashed-password> # key/value pair for username and password
        ```

        ```console
        # then restart the node_exporter
        sudo systemctl restart node_exporter
        ```

    * In `prometheus` update the yaml to include this authentication

        ```yml
        scrape_configs:
        - job_name: "node"
        # ... other configs ...
          basic_auth:
            username: prometheus
            password: <plaintext_password>
            password_file: <file-with-password>
        ```

        ```console
        # restart prometheus
        sudo systemctl restart prometheus
        ```
    
    ##### Encryption
    
    * Also by default, there is no encryption between the 2 services, so its all plaintext

        ```console
        # make config dir for node_exporter
        sudo mkdir /etc/node_exporter
        cd /etc/node_exporter
        # you can use letsencrypt or any other CA
        # or create self signed certs for this
        # ideal to have a ca cert that you use to create the certs for each exporter
        sudo openssl req -new -newkey rsa:2048 -days 365 -nodes -x509 -keyout node_exporter.key -out node_exporter.crt -subj "/C=US/ST=California/L=Oakland/O=MyOrg/CN=localhost" -addext "subjectAltName = DNS: localhost" 

        # create a config.yml to customize behavior of node_exporter
        cat >> config.yml
        tls_server_config: 
            cert_file: /etc/node_exporter/node_exporter.crt # these certs would be in /etc/ssl/certs
            key_file: /etc/node_exporter/node_exporter.key

        # set permissions
        sudo chown -R node_exporter: /etc/node_exporter

        # edit the systemd file to include config.yaml
        ExecStart=/usr/local/bin/node_exporter --web.config=/etc/node_exporter/config.yml
        sudo systemctl daemon-reload

        # restart the service
        sudo systemctl restart node_exporter

        # now try to curl, since we used the self signed cert, we need to pass the -k flag
        curl -k https://localhost:9100/metrics
        ```

    * Now we need to configure `prometheus` to use this cert to authenticate with the exporter

        ```console
        # if you created in node_exporter node, copy it over to prometheus
        scp <exporter-host-ssh-config>:/etc/node_exporter/node_exporter.crt /etc/prometheus
        chown prometheus: /etc/prometheus/node_exporter.crt
        ```

        ```yml
        # update prometheus.yml
        scrape_configs:
        - job_name: "node"
          scheme: https
          tls_config:
            ca_file: /etc/prometheus/node_exporter.crt # could be root CA
            insecure_skip_verify: true # because its a self-signed cert
        static_configs:
        - targets: ["node01:9100"]
          labels:
            node: "node01"
        ```

        ```console
        # restart prometheus
        sudo systemctl restart prometheus
        ```

    * NOTE: in real-life use cases. generate a root and sign all the certs for the exporter
        - Follow [this](https://learn.microsoft.com/en-us/azure/application-gateway/self-signed-certificates) doc
    * NOTE: mTLS is also a more secure way to do this
        - Follow [this](https://nsrc.org/workshops/2025/apricot/virt/netmgmt/en/prometheus/ex-security.html) doc


- #### Changes to Prometheus Configs

    * When you make changes to the configs, `prometheus` won't pick these up automatically. So you'll need to restart the process

        ```console
        # bare metal/vm
        ctrl+c -> ./prometheus

        # systemd
        kill -HUP <pid> 
        --or--
        sudo systemctl restart prometheus
        ```

    * Once you've updated these configs and added a new target, for instance, you can access the webUI for `prometheus` and click Status -> Target Health
        - The new target you added should appear

