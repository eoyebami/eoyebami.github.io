## Introduction to Prometheus
- [Overview](#overview)
- [Basics](#basics)
- [Architecture](#architecture)

### Overview

* `Prometheus` was created as a way to aggregate and collect metrics
  - For instance, say we have an app that uploads files that the user provides
    * But we identify that theres a issue with users upload files that may be too large
    * We want to figure out at what size the application starts to degrade
    * Prometheus can allow us to retrieve the avg file sizes and avg latency, which can be plot to show degradation

### Basics

* Prometheus is an open-source monitoring tool that collects, visualizes, and aggregates metric data
  - It slaos allows you to generate alerts when metrics reach a specified threshold
* Prometheus collects metrics by scraping targets who expose metrics through an HTTP endpoint (a pull based model)
  - The application we want to collect metrics from will host those metrics on a HTTP endpoint and we send a `GET` request to retrieve those metrics
  - Scraped metrics are then store in a timeseries db which can be queried using Prometheus' builtin query language, `PromQl`
* It is designed to monitor time-series data that is numeric 
  - `Time Series` data is a sequence of data points recoreded at specific, ordered time intervals
  - It tracks changes, trends, and patterns over time rather than providing a static snapshot
  * Things like events, system logs, and traces CANNOT be monitored by Prometheus

* Metrics Prometheus can monitor include:
  1. CPU/Memory Utilization
  2. Disk Space
  3. Service Uptime
  4. Application specific data:
    - Number of exceptions
    - Latency
    - Pending requests

* NOTE: prometheus uses a `pull based model` which is ideal for metrics aggregation
  - For `event driven observation` something like ElasticSearch (`push based system`) is a better solution 

### Architecture

* Within the main Prometheus there are multiple Components:
- `Main Component`, which comprises of 3 parts:
  1. `Data Retrieval Worker`: responsible for collecting the metrics from target by sending the `GET` request to said target
    - This request by default is sent to `/metrics` endpoint at each target
    - Although this endpoint can be configured to use a different path
  2. `Timeseries Database`: once worker collects metrics it is stored within this db
  3. `HTTP Service`: there to allow us to retrieve data sent to the db through `PromQl` query

- `Exporters`, which are processes that run on the prometheus targets, are responsible for serving up the metrics so that the retrieval worker and pull them
  * Takes internal data and converts it into a metric that prometheus can understand 
  * Most systems don't collect metrics and expose them at an endpoint to be consumed by prometheus so an `exporter` will need to be installed
  * Prometheus native exporters include:
    1. `Node Exporters (linux servers)`
    2. `Windows`
    3. `MySQL`
    4. `Apache`
    5. `HAProxy`
  * For monitor applications metrics for custom applications, you can use `prometheus client libraries`
    - Which allow you to expose any app metrics you need tracked
    - Which support a variety of different programming languages

- `Push Gateway`, are for when we have a `short lived job # do not run long enough for prometheus to be able to scrape it`
  * `Short lived job` is going to be used to send the data to a `push gateway`
  * Which prometheus can then query to retrieve metrics

- `Service Discovery`:
  * Prometheus needs to be able to know about all the targets, which can be configured within the conf file
  * But for dynamic enviroments like a `csp` or `kubernetes`, you also want to be able to dynamically configure these targets as well
  * `Service Discovery` allows you to provide a list of targets to be scraped so you don't need to manually do so yourself

- `Alert Manager`:
  * Although prometheus can trigger alerts, it does not actually send the notifications
  * It sends the alert to an `Alert Manager`, which will then be responsible for sending those notifications

- `Promethus Web UI/Grafana`:
  * User interface that allows us to interact with Prometheus via its query language via its HTTP services
