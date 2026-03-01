## Prometheus Installation
- [Prometheus Installation](#prometheus-installation)
- [Node Exporter Installation](#node-exporter-installation)

### Prometheus Installation

- #### Bare Metal/VM Installation

  * First we need to navigate to the prometheus site to grab the [download url](https://prometheus.io/download) and then we can run a `wget` and begin installation 
  
    ```console 
    wget https://github.com/prometheus/prometheus/releases/download/v2.37.0/prometheus-2.37.0.linux-amd64.tar.gz # pull download tar
    tar -xvzf prometheus-2.37.0.linux-amd64.tar.gz # untar 
    cd prometheus-2.37.0.linux-amd64.tar.gz 
    ```
  
  * Within the untarred directory you'll see the following:
  
    ```console
    $ ls -la
    total 206204
    drwxr-xr-x.  4 eoyebami domain users       132 Jul 14  2022 .
    drwx------. 30 eoyebami domain users      4096 Feb 13 15:20 ..
    drwxr-xr-x.  2 eoyebami domain users        38 Jul 14  2022 console_libraries # for dashboard and visualization
    drwxr-xr-x.  2 eoyebami domain users       173 Jul 14  2022 consoles # for dashboard and visualization
    -rw-r--r--.  1 eoyebami domain users     11357 Jul 14  2022 LICENSE
    -rw-r--r--.  1 eoyebami domain users      3773 Jul 14  2022 NOTICE
    -rwxr-xr-x.  1 eoyebami domain users 109655433 Jul 14  2022 prometheus # executable
    -rw-r--r--.  1 eoyebami domain users       934 Jul 14  2022 prometheus.yml # configuration file
    -rwxr-xr-x.  1 eoyebami domain users 101469395 Jul 14  2022 promtool # cli utility which can be used to validate configuration file before starting prometheus
    ```
  
  * On a bare metal server or a vm, you can start the prometheus application by just running the executable: `./prometheus`
    - Web UI will be accessible at `http://localhost:9090`
    - By default prometheus is configured to monitor itself, you can type `up` in the query box to confirm this
      * This should output: `up{instance="localhost:9090", job="prometheus"}         1`, the `1` denotes that target is up and running

- #### Systemd Installation
  
  * First thing we want to do is create a user called `prometheus` that will actually run the process

    ```console
    sudo useradd --no-create-home --shell /bin/false prometheus # no home dir and user cannot login

    sudo mkdir /etc/prometheus # for configuration files for promethus
    sudo mkdir /var/lib/prometheus # for data for prometheus

    sudo chown prometheus: /etc/prometheus
    sudo chown prometheus: /var/lib/prometheus

    # download binary
    wget https://github.com/prometheus/prometheus/releases/download/v2.37.0/prometheus-2.37.0.linux-amd64.tar.gz # pull download tar
    tar -xvzf prometheus-2.37.0.linux-amd64.tar.gz # untar 
    cd prometheus-2.37.0.linux-amd64.tar.gz 

    # move executables
    sudo cp prometheus /usr/local/bin/
    sudo cp promtool /usr/local/bin/
    sudo chown prometheus: /usr/local/bin/prometheus
    sudo chown promethus: /usr/local/bin/promtool

    # move configurations files
    sudo cp -r consoles /etc/prometheus
    sudo cp -r consoles_libraries /etc/prometheus
    sudo cp prometheus.yaml /etc/promethus/
    sudo chown -R prometheus: /etc/prometheus
    ```

  * Now that we've moved all these files, the command to run prometheus will be much different than just running the executable

    ```console
    sudo -u prometheus /usr/local/bin/promethus \
      --config.file /etc/prometheus/prometheus.yml \
      --storage.tsdb.path /var/lib/prometheus \
      --web.console.templates=/etc/promethus/consoles \
      --web.console.libraries=/etc/prometheus/console_libraries
    ```

  * With this in place, we can create the systemd file. More info on that can be found [here](../../linux/2023-04-27-systemd.md)

    ```ini
    [Unit]
    Description=Prometheus
    Wants=network-online.target # service only starts when network is up
    After=network-online.target

    [Service]
    User=prometheus
    Group=prometheus
    Type=simple
    ExecStart=/usr/local/bin/promethus \
      --config.file /etc/prometheus/prometheus.yml \
      --storage.tsdb.path /var/lib/prometheus \
      --web.console.templates=/etc/promethus/consoles \
      --web.console.libraries=/etc/prometheus/console_libraries
    ExecStop=kill -s <SIGNAL> <PID> # write some script to generate this dynamically
    [Install]
    WantedBy=multi-user.target # starts prometheus at normal system setup
    ```

  * Now we can launch the service

    ```console
    sudo systemctl daemon-reload # since we changed/added service files
    sudo systemctl start prometheus
    sudo systemctl status prometheus
    sudo systemctl enable prometheus # allows service to start automatically on booth
    ```

### Node Exporter Installation

- #### Bare Metal/VM Installation

  * First we need to navigate to the prometheus site to grab the [download url](https://prometheus.io/download/#node_exporter) and then we can run a `wget` and begin installation 

    ```console 
    wget https://github.com/prometheus/node_exporter/releases/download/v1.10.2/node_exporter-1.10.2.linux-amd64.tar.gz # pull download tar
    tar -xvzf node_exporter-1.10.2.linux-amd64.tar.gz # untar 
    cd node_exporter-1.10.2.linux-amd64.tar.gz
    ```

  * Within the untarred directory you'll see the following:
  
    ```console
    $ ls -la
    total 206204
    drwxr-xr-x.  4 eoyebami domain users       132 Jul 14  2022 .
    drwx------. 30 eoyebami domain users      4096 Feb 13 15:20 ..
    -rw-r--r--.  1 eoyebami domain users     11357 Jul 14  2022 LICENSE
    -rw-r--r--.  1 eoyebami domain users      3773 Jul 14  2022 NOTICE
    -rwxr-xr-x.  1 eoyebami domain users 109655433 Jul 14  2022 node_exporter # executable
    ```

  * On a bare metal server or a vm, you can start the node_exporter by just running the executable: `./node_exporter`
    - Will be accessible at `http://localhost:9100`
    - Will print out all of the metrics if you curl the endpoint `curl localhost:9100/metrics`
      * Remember both port and endpoint are configurable

- #### Systemd Installation

  * First thing we want to do is create a user called `node_exporter` that will actually run the process

    ```console
    sudo useradd --no-create-home --shell /bin/false node_exporter # no home dir and user cannot login

    # download binary
    wget https://github.com/prometheus/node_exporter/releases/download/v1.10.2/node_exporter-1.10.2.linux-amd64.tar.gz # pull download tar
    tar -xvzf node_exporter-1.10.2.linux-amd64.tar.gz # untar 
    cd node_exporter-1.10.2.linux-amd64.tar.gz

    # move executables
    sudo cp node_exporter /usr/local/bin/
    sudo chown node_exporter: /usr/local/bin/prometheus
    ```

  * Now you can run the executable azs that user

    ```console
    sudo -u node_exporter /usr/local/bin/node_exporter 
    ```

  * With this in place, we can create the systemd file. More info on that can be found [here](../../linux/2023-04-27-systemd.md)

    ```ini
    [Unit]
    Description=Node Exporter
    Wants=network-online.target # service only starts when network is up
    After=network-online.target

    [Service]
    User=node_exporter
    Group=node_exporter
    Type=simple
    ExecStart=/usr/local/bin/node_exporter
    ExecStop=kill -s <SIGNAL> <PID> # write some script to generate this dynamically
    [Install]
    WantedBy=multi-user.target # starts node_exporter at normal system setup
    ```

  * Now we can launch the service

    ```console
    sudo systemctl daemon-reload # since we changed/added service files
    sudo systemctl start node_exporter
    sudo systemctl status node_exporter
    sudo systemctl enable node_exporter # allows service to start automatically on booth
    ```