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
