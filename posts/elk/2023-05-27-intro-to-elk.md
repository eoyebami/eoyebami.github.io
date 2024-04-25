<h1>What is ELK</h1>
* ELK stack is an open-source software stack that is used for centralizing logging and data analyis. It is an acronym:
  - Elasticsearch: A distributed anlyics engine designed to store, search, and analyze large volumes of data in real-time
    * A NoSQL JSON based Data Store (DB)
    * Uses RESTFUL APIs for interactions
    * Elasticsearch uses something called shards: which is data divided into multiple manageable units. Sharding is the process of horizontally splitting an index into multiple smaller index shards, allowing Elastic to distribute the data and workload across multiple nodes in a cluster. 
    - Each shard is an independent subset of data containing a portion of the index's documents and their inverted index
 Types of Shards:
  * Primary Shards: when you create an index, you specify the number of primary shards it should have. Each primary shard is responsible for storing a subset of an index's data. 
    - By default Elastic creates 5 primary shards for an index. 
    - Each shard is distributed across the cluster on separate nodes, which allows Elastic to parallelize the indeing and search operations
  * Replica Shards: to ensure high availability, elastic allows you to create replica shards for each primary shard
    - Each replica is an exact copy of the primary shard, which serve as backups and are also split across all nodes (different nodes than their associated primary shard)
    - If a node hosting a primary shard goes down, the replica shard assigned to that node will take over and serve requests from one of the other available noes.
    
 Compare and Contrast to Relation Databases:

| RDMS    | ElasticSearch   |
|---      |---              |
| DBs     | Indexes/Indices | 
| Tables  | Patterns/Types  |
| Rows    | Documents       |
| Columns | Fields          |

  - Logstash: A data processing pipeline that ingests data from multiple sources, transforms it, and sends it to various destinations. It collects logs, metrics, and other event data from various sources
    * Inputs, transformers, and stash data (output)
    * Beats: multiple light weight datacollectors that are similar to logstash (ex: Filebeat, MetricBeat, whcih are produced by Elastic itself, or other open-source DCs like `Fluentd`; which is similar to `Beats`)
    - They collect data and ship them to software like ElasticSearch and/or Logstash for further processing
    - They don't filter the data; they act like agents and ship data to Elastic
  - Kibana: a data visualizer
    * A web based UI, how you interact with all the data that ElasticSearch prepares and indexes for you to use
    * Can build Dashboards, widgets, and visualizations of your data; that will continue to update as ElasticSearch receives them 
* Flow Chart:
  - Logstash (or Beats, for a lightweight alternative; EKS) -> ElasticSearch -> Kibana
    * Beats are attached to remote servers (as agents) to collect information from various sources and ship data to Logstash for filtration or directly to ElasticSearch for analysis
    * Data is stored in the ElasticSearch DS 
    * Kibana checks where ElasticSearch is and collects the data itself
* Features of ELK
  - System Performance Monitoring
  - Log Management
  - Application Performance Monitoring (APM)
  - Security Monitoring and Alerting
  - Data Visualization 
* ELK installation:
  - Prereq.:
    * 2GB of memory
    * 20GB of storage
    * Java

```bash
# Update ubuntu server and Install Java 8
sudo apt-get update && sudo apt-get upgrade -y
sudo apt-get install -y openjdk-8-jdk
# Installing java 11
sudo apt install default-jdk -y
java -version

# Managing Java versions
sudo update-alternatives --config java (lists all versions)
export jAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64/bin/java

# Install nginx and enable it; this will allow us to access kibana, because it cannot be accessed by the ouside world
sudo apt-get update
sudo apt-get -y install nginx
sudo systemctl enable nginx

# Installing ElasticSearch 
curl -fsSL https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo gpg --dearmor -o /usr/share/keyrings/elastic.gpg
echo "deb [signed-by=/usr/share/keyrings/elastic.gpg] https://artifacts.elastic.co/packages/7.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-7.x.list
sudo apt update
sudo apt install elasticsearch

# Installing Logstash 
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
sudo apt-get install apt-transport-https
echo "deb https://artifacts.elastic.co/packages/8.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-8.x.list
sudo apt-get update && sudo apt-get install logstash

# Installing Filebeat
sudo apt-get update && sudo apt-get install filebeat
sudo systemctl enable filebeat

# Installing Kibana
sudo apt update && sudo apt install kibana
sudo systemctl enable kibana
```

