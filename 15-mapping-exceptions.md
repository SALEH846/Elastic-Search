- A mappings essentially entails two parts
    1. Process: Defining how JSON documents will be stored
        - Explicit mapping: Fields and their types are pre-defined
        - Dynamic Mapping: Fields and their types automatically defined by ElasticSearch
    2. Result: The actual metadata resulting from the definition process

- A mapping example
```json
{
    "mappings": {
        "properties": {
            "timestamp": { "type": "date" },
            "service": { "type": "keyword" },
            "host_ip": { "type": "ip" },
            "port": { "type": "integer" },
            "message": { "type": "text" }
        }
    }
}
```

- Mapping Challenges
    - Explicit mapping: Mapping exceptions when there's a mismatch
    - Dynamic mapping: May lead to mapping explosion

- Hands on practice
- Create a new index
```shell
curl --request PUT 'http://localhost:9200/microservice-logs' \
--data-raw '{
   "mappings": {
       "properties": {
           "timestamp": { "type": "date"  },
           "service": { "type": "keyword" },
           "host_ip": { "type": "ip" },
           "port": { "type": "integer" },
           "message": { "type": "text" }
       }
   }
}'
```
- A well defined json for this mapping would look something like this
```json
{
    "timestamp": "2020-04-11T12:34:56.789Z", 
    "service": "ABC", 
    "host_ip": "10.0.2.15", 
    "port": 12345, 
    "message": "Started!" 
}
```
- Now, let's insert a document in the index which has valid integer given as string for `port` field instead of integer. Since it can be converted to integer from string, it will work
```shell
curl --request POST 'http://localhost:9200/microservice-logs/_doc?pretty' \
--data-raw '{"timestamp": "2020-04-11T12:34:56.789Z", "service": "XYZ", "host_ip": "10.0.2.15", "port": "15000", "message": "Hello!" }'
```
- Now, insert another document with `port` field given as string instead of integer which is not a valid integer, it will not work and `number_format_exception` will be raised
```shell
curl --request POST 'http://localhost:9200/microservice-logs/_doc?pretty' \
--data-raw '{"timestamp": "2020-04-11T12:34:56.789Z", "service": "XYZ", "host_ip": "10.0.2.15", "port": "NONE", "message": "I am not well!" }'
```
- One way to solve this is to set `ignore_malformed` property for the `port` field to `true`. But for that to work, you have to first close the index, make the change and the reopen the index
```shell
curl --request POST 'http://localhost:9200/microservice-logs/_close'
 
curl --location --request PUT 'http://localhost:9200/microservice-logs/_settings' \
--data-raw '{
   "index.mapping.ignore_malformed": true
}'
 
curl --request POST 'http://localhost:9200/microservice-logs/_open'
```
- Now, re-insert the same document again. This time it will work
```shell
curl --request POST 'http://localhost:9200/microservice-logs/_doc?pretty' \
--data-raw '{"timestamp": "2020-04-11T12:34:56.789Z", "service": "XYZ", "host_ip": "10.0.2.15", "port": "NONE", "message": "I am not well!" }'
```
- Check the inserted document, you will see that the `port` field is ignored
```shell
curl http://localhost:9200/microservice-logs/_doc/NYKJNZAB-IT-K0_GkBIR?pretty
```
- However, `ignore_malformed` cannot handle JSON on input. The following will throw an error.
```shell
curl --request POST 'http://localhost:9200/microservice-logs/_doc?pretty' \
--data-raw '{"timestamp": "2020-04-11T12:34:56.789Z", "service": "ABC", "host_ip": "10.0.2.15", "port": 12345, "message": {"data": {"received":"here"}}}'
```
- Intead of passing JSON for the `message` field. We can introduce a new field named `payload` for JSON and that field will get dynamically mapped
```shell
curl --request POST 'http://localhost:9200/microservice-logs/_doc?pretty' \
--data-raw '{"timestamp": "2020-04-11T12:34:56.789Z", "service": "ABC", "host_ip": "10.0.2.15", "port": 12345, "message": "Received...", "payload": {"data": {"received":"here"}}}'
```
- To see the mapping, run the command
```shell
curl --request GET http://localhost:9200/microservice-logs/_mappings?pretty
```
- But the problem now is that the mapping has been created dynamically for `payload` field. But in real world `payload` can consist of JSON of various different formats. So, it we try to insert a new document with some slightly different JSON for `payload` field then it will not work
```shell
curl --request POST 'http://localhost:9200/microservice-logs/_doc?pretty' \
--data-raw '{"timestamp": "2020-04-11T12:34:56.789Z", "service": "ABC", "host_ip": "10.0.2.15", "port": 12345, "message": "Received...", "payload": {"data": {"received": {"even": "more"}}}}'
```

- One other thing to take note of is limit of the number of fields. By default, only 1000 fields are allowed in a document. If document contains more than 1000 fields then insertion will fail.
- The limit can be increased using the command
```shell
curl --location --request PUT 'http://localhost:9200/big-objects/_settings' \
--data-raw '{
    "index.mapping.total_fields.limit": 1001
}'
```