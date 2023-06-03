<h1>Logstash Pipeline: Inputs</h1>
Logstash collects, process, and transforms log data from various sources before shipping to ElasticSearch for storage and analysis. 
* A logstash pipeline is a configuration file or set of configuration files that define how Logstash should handle incoming data. It consists of a series of plugins, as follows:
  - input 
  - filter
  - output
* You can create a pipeline configuration file for various types of application logs in the `/etc/logstash/conf.g` directory
  - All your pipeline configuration files can be referenced in the `pipeline.yml` file
<h3>Pipeline Configuration Inputs</h3>
The input option specifies where Logstash will be retrieving the data from, it can also specify what do when its finished processing that data
* First retrieve the sample logs:
  - `wget https://logz.io/sample-data`
* Create `apachelog.conf` in the `/etc/logstash/conf.d`
```
input {
  # Specify the input source, such as a file, syslog, or Beats input
  file {
    path => "/var/log/logstash/apache.log"
    start_position => "beginning"
    sincedb_path => "/var/log/logstash/apache-sincedb"
    sincedb_write_interval => 5
    file_completed_action => "log"
    file_completed_log_path => "/var/log/logstash/completed_logs/apache_completed.log"
  }
}
```
* <h5>Pipeling Configuration Input: Options</h5> 
  - Specify an input source: (files, beats input, syslog, tcp/udp inputs, http, etc)
    * There are multiple different sources, but file sources are the most commonly used for applications. This will be the main use case for this example
  - `path => "/path/to/log_file"` : give path to your logfile
    * Make sure its a directory that logstash can access such as a `/var/log/*` directory
  - `start_position => "beginning"` : where you want logstash to start reading the input data, and it can have the following values:
    * `beginning`: Logstash will start reading from the beginning
    * `end`: Logstash will start reading from the end, only new lines appended will be read
    * `file_pointer`: Logstash will start reading the file from a specific byte offset, which is determined by the `sincedb_path` setting in this plugin. 
  - `sincedb_path => /path/to/sincedv/file`: path to sincedb file
    * By default logstash uses a `sincedb file` for each file in the `path` option of the input plugin, to keep track of where it left off 
  - `sincedb_write_interval => 15` : how frequently sincedb file is updated with current read postion
    * by default it is set to 15 seconds
  - `file_completed_action => "delete"` : specifies which action to take after the file is fully processed
    * `delete` : deletes file
    * `log` : Logstash will log a message indicating the file has been fully processed in its own logstash log file
    * `nothing` : this is the default, logstash will do nothing
    * `file_completed_log_path =>` must be specified when using this option that contains the values `log` or `log_delete`
  -`file_completed_log_path => "/home/ubuntu/apache_completed.log"` : specifies the path to the file where logstash will write all complete logs
  - `discover_interbal => 15` : controls how often logstash checks for new files matching the `path` pattern
    * default is 15 seconds
  - `ignore_older => "2hr"` : ignores files that have not been modified for a specified time 
    * can define them using values `(10m, 1hr, 1d)`
  - `close_older => "1d"` : closes files that have not been read for a specified amount of time
  - `exclude => ["*.gz", "old_logs.log"]` : will not read log files specified here
* for more input plugin options go to [elastic-input-plugins](https://www.elastic.co/guide/en/logstash/current/input-plugins.html)
