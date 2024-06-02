<h1>Logstash Pipeline: Filter Plugin</h1>
The filter plugin is a powerful component that allows you to perform transformations and manipulations on incoming events
<h3>Logstash Pipeline: Filter</h3>
```
filter {
  # Apply various filters to process and transform the incoming data
  fingerprint {
    source => ["source_ip"]
    target => "[@metadata][fingerprint]"
    method => "MURMUR3"
  }
  grok {
    match => { "message" => "%{COMBINEDAPACHELOG}" }
  }
  date {
    match => [ "timestamp", "YYYY-MM-dd HH:mm:ss Z"]
    target => "@timestamp"
    locale => "en"
  }
  # Additional filters can be added based on your requirements
}
```

* <h5>Pipeline Configuration Filter: Options</h5>
  - `date {}` : with this plugin you can parse timestamps from a from fields, and then using that date as the logstash timestamp for the event
    * `local => "en"` : used to set the date strings in different locales 
    * `match => [ "timestamp", "dd/MMM/YYYY:HH:mm:ss Z"]` : setting the field which the date is exrtacted and the expected format of the string (more info on format at [date-format](https://www.elastic.co/guide/en/logstash/current/plugins-filters-date.html) 
    - Logdates usually follow format (`yyyy-MM-dd`),
    - Timestamps usually have both date and time information
    * `target => "@timestamp"` : stores the matching timestamp into the given target field
  - `fingerprint {}` : allows you to generate a unique hash value based on the contents of one or more fields. This is used to identify duplications, great for indexing becuase you can index on generated fingerprint
    * `source => ["source_ips]` : specifies the field(s) from which the fingerprint will be generated
    * `target => "[@metadata][fingerprint]"` : specified the field name where the fingerprint will be stored; this hash will be stored in the metadata field of the log event. 
    * `method => "MURMUR3"` : what method will be used to create the hash
  - `grok {}` : allows you to parse and extract structured fields from unstructured log data. It defines pattern and assigns them to named fields. `grok will attempt to match the log line against the defined patterns.
