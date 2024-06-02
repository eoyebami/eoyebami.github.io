<h1>Navigating Kibana: Index Patterns</h1>
After setting up your pipeline to stash data from logstash into ElasticSearch. You can use kibana as a way to visualize the data.
<h3>Creating Index Patterns</h3>
* Login to your Kibana 
  - `http://1p-addr:5601`
* Go into -> Stack Management -> Kibana -> Index Patterns
  - This block will allow you to create index patterns from all the sources of data in ElasticSearch. You create index patterns based on existing indices in ElasticSearch
  - After creating the index pattern, Kibana analyzes the data in the indices associated with the pattern to determine the available fields and their data types. 
  - The index patterns are dynamic, meaning they can include new indices that match the pattern as they are created. 
* Give the index pattern a name
  - This name should match any of the sources contained in ElasticSearch
  - If a source is `apache-logs-2023.06.03` and you want to capture any sources that have the prefix `apache-logs` then the name of the index pattern will be `apache-logs*`
* Give the index pattern a timestamp field
  - `@timestamp` : a reserved field used in systems like ELK, it represents the timestamp of the event and is automatically populated
    * Does not need the `date plugin`. Logstash and Elasticsearch will auto recognize alldate formatted using `ISO 8601`
    * ex: `YYYY-MM-DD HH:mm:ss,SSSZ`
  - `@logdate` : is not a reserved field, it is not automatically populated; needs to be parsed with the `date plugin`. 
* Create the Index Pattern
<h3>Viewing the Index Pattern</h3>
* Go into Discover and Change the selected Index Pattern 
  - In this case select the `apache-test-logs*` you created
* All the logs, filtered and parsed as specified in your configuration file, should be specified here
