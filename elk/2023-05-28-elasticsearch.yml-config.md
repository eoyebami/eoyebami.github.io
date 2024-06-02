<h1>Configuring ElasticSearch: elasticsearch.yml</h1>
<h3>Installation</h3>
```
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
```

<h3>ElasticSearch.yml File Configuration</h3>
* TO configure your elasticsearch, you need to modify the `elasticsearch.yml` file located in `/etc/elasticsearch` directory. Here you can set the config values as follows:
  - `cluster.name: my-elasticsearch` : sets the name of the Elasticsearch cluster
    * All nodes with the same cluster name will join the same cluster
  - `node.name: my-node0` : sets the name of the Elasticsearch node
  - `node.attr.rack: rack-1` : assigns the node to a specific rack, which can be used by Elastic for determine shard allocations
    * You can assign replica shards to another node in the same rack (maybe one in the same AZ or region; to ensure fast response if the node hosting the primary shard, goes down)
    * Useful at 3 or more nodes per rack
  - `network.host: 0.0.0.0` : determines which network interface or IP address to which Elastic search binds
    * In a CSPs case, if you place the private ip of the node as its network host, only other vms in its VPC/network can access it
    * Using `localhost` or `127.0.0.1` will restrict access to itself
    * Binding to the public ip address, will make the Elasticsearch accessible over the internet
    * Using `0.0.0.0` will make Elasticsearch accessible from both private network or public internet (both public and private ip address of the instance
  - `http.port: 9200` : sets the port on which Elasticsearch listens for the HTTP traffic
  - `path.data: /var/lib/elasticsearch` : specifies the location elasticsearch stores its data (indices, shards, etc.)
  - `path.logs: /var/log/elasticsearch` : specifies the location where the elasticsearch writes its log files
  - `bootstrap.memory_lock: true` : used to lock the process mem or Elastic on the server's RAM
    * Will not use swap space
    * You will need to lock memory first
  - `discovery.seed_hosts: ["host1", "host2"]` : used to specify an initial list of hosts that a new node uses to discover and join the cluster
    * if this is the first node in the cluster, set this to an empty set
    * allows you to define a list of one or more seed hosts that the node can use as points of contact for cluster discovery
    * new node uses seed hosts to retreive cluster metadata
  - `cluster.initial_master_nodes: ["node-1", "node-2"]` : list of nodes, you want the cluster to choose a master node from
    * If this is the firsy node in the cluster; set this to the node itself
  - `action.destructive_requires_name: true` : must explicit name the resource in certain destructive operations, like deleting an index, closing an index. or deleting a snapshot
* More information on ElasticSearch configuration can be found at [Configuring-Elastic](https://www.elastic.co/guide/en/elasticsearch/reference/current/settings.html)
* After configuration is complete run: `sudo systemctl restart elasticsearch`
  - `sudo systemctl status elasticsearch.service`
