### Installation of ElasticSearch 8
- All the instructions are provided [here](http://media.sundog-soft.com/es8/install.txt)

### Elastic Stack
- Elasticsearch is just one piece of this system
    - Horizontally scalable search engine
    - Each "shard" is an inverted index of documents
    - But not just for full text search
    - Can handle structured data, and can aggregate data quickly
    - Often a faster solution than Hadoop/Spark/Flink etc.
- Kibana
    - Web UI for searching and visualizing
    - Complex aggregations, graphs and charts
    - Often used for log analysis
- Logstash / Beats
    - Ways to feed data into elasticsearch
    - FileBeat can monitor log files, parse them, and import into elasticsearch in near real-time
    - Logstash also pushes data into elasticsearch from many machines
    - Not just log files
- X-Pack
    - Security
    - Alerting
    - Monitoring
    - Reporting
    - Machine Learning
    - Graph Exploration