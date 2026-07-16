## Opensearch
- [Overview](#overview)
- [Components](#components)
- [Features](#features)
- [Hands On](#hands-on)

### Overview

![alt text](images/opensearch/image.png)
* AWS `Opensearch` is a tool that allows developers to ingest. seach, visualize and analyze large volumes of data
    - derived from `elasticsearch` after `elasticsearch` went private

### Components

![alt text](images/opensearch/image-1.png)

* `Opensearch` has both service and serverless options
* `Opensearch Ingestion` is an `opensearch` datacollector that delivers real time log and metrics data to opensearch service domains or serverless collections
    - each cluster you make in `opensearch` will have its own service domain
    - replaces `logstash` and `jaeger` by configuring the pipelines that send data from the producers over to the db

### Features

![alt text](images/opensearch/image-2.png)

### Hands On

1. Create a Domain (Cluster)
    - ![alt text](images/opensearch/image-3.png)

2. Specify templates and deployment options
    - ![alt text](images/opensearch/image-4.png)

3. Specify engine options
    - ![alt text](images/opensearch/image-5.png)

4. Specify data nodes
    - ![alt text](images/opensearch/image-6.png)
    - ![alt text](images/opensearch/image-7.png)

5. Define extra configuration
    - ![alt text](images/opensearch/image-8.png)

6. Define networking
    - ![alt text](images/opensearch/image-9.png)

7. Define fine grain access control and saml/cognito settings
    - ![alt text](images/opensearch/image-10.png)
    - ![alt text](images/opensearch/image-11.png)

8. Define access policy 
    - ![alt text](images/opensearch/image-12.png)

9. Encryption
    - ![alt text](images/opensearch/image-13.png)

10. Once deployed you'll see the cluster configuration
    - ![alt text](images/opensearch/image-14.png)
        * both dashboard (kibana) and domain endpoint (opensearch) are available