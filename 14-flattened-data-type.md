- Up to this point, we have only handled document with a few associated fields.
- However, if we have to deal with objects having many nested subfields then the elasticsearch's performance start to suffer.
- To solve this elasticsearch offers a `flattened` datatype.
- Create a new index and insert a document in it
```shell
curl -XPUT "http://127.0.0.1:9200/demo-default/_doc/1" -d'{
  "message": "[5592:1:0309/123054.737712:ERROR:child_process_sandbox_support_impl_linux.cc(79)] FontService unique font name matching request did not receive a response.",
  "fileset": {
    "name": "syslog"
  },
  "process": {
    "name": "org.gnome.Shell.desktop",
    "pid": 3383
  },
  "@timestamp": "2020-03-09T18:00:54.000+05:30",
  "host": {
    "hostname": "bionic",
    "name": "bionic"
  }
}'
```
- Let's see what mappings are created by default for `demo-default` index.
```shell
curl -XGET "http://127.0.0.1:9200/demo-default/_mapping?pretty=true"
```
- Get the cluster state
```shell
curl -XGET "http://127.0.0.1:9200/_cluster/state?pretty=true" >> es-cluster-state.json
```

- When an elasticsearch server goes down due to a large number of mappings due to large number of nested subfields in the documents then this is called **mapping explosion**.
- To solve this, elasticsearch offers a `flattened` data type. Essentially, what this data type does is that it maps the entire object with its fields into a single field.

- To see the `flattened` data type in action, let's create a new index for the same document.
```shell
curl -XPUT "http://127.0.0.1:9200/demo-flattened"
```
- Create mapping for the index
```shell
curl -XPUT "http://127.0.0.1:9200/demo-flattened/_mapping" -d'{
  "properties": {
    "host": {
      "type": "flattened"
    }
  }
}'
```
- Put the document in the index
```shell
curl -XPUT "http://127.0.0.1:9200/demo-flattened/_doc/1" -d'{
  "message": "[5592:1:0309/123054.737712:ERROR:child_process_sandbox_support_impl_linux.cc(79)] FontService unique font name matching request did not receive a response.",
  "fileset": {
    "name": "syslog"
  },
  "process": {
    "name": "org.gnome.Shell.desktop",
    "pid": 3383
  },
  "@timestamp": "2020-03-09T18:00:54.000+05:30",
  "host": {
    "hostname": "bionic",
    "name": "bionic"
  }
}'
```
- Now, check the mappings of the above index
```shell
curl -XGET "http://127.0.0.1:9200/demo-flattened/_mapping?pretty=true"
```
- Only the host property got `flattened` data type. All other are the same as before.
- Now, add more fields to `host` field and see what happens
```shell
curl -XPOST "http://127.0.0.1:9200/demo-flattened/_update/1" -d'{
    "doc" : {
        "host" : {
          "osVersion": "Bionic Beaver",
          "osArchitecture":"x86_64"
        }
    }
}'
```
- Check if all the fields are added or not
```shell
curl -XGET "http://127.0.0.1:9200/demo-flattened/_search?pretty=true" -d'{
  "query": {
    "term": {
      "host": "Bionic Beaver"
    }
  }
}'
```
- There are points to be taken note of when using `flattened` data type
- The fields of the flattened data type object will be treated as keywords -- in elasticSearch it means no analyzers and tokenizers will be applied to flattened fields -- more limited search capability.
- Using an exact match -- you will get the result
```shell
curl -XGET "http://127.0.0.1:9200/demo-flattened/_search?pretty=true" -d'{
  "query": {
    "term": {
      "host.osVersion": "Bionic Beaver"
    }
  }
}'
```
- Using partial match will not work
```shell
curl -XGET "http://127.0.0.1:9200/demo-flattened/_search?pretty=true" -d'{
  "query": {
    "term": {
      "host.osVersion": "Beaver"
    }
  }
}'
```

#### Supported queries for flattened data type
- tems, terms, terms_set
- prefix
- range (non numerical range operations)
- match and multi match (we have to supply exact keywords)
- query_string and simple_query_string
- exists