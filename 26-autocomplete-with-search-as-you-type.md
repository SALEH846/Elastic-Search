### Search As You Type DataType

- If you mark a field with `search_as_you_type` data type then ES will create several subfields for that field.
- The Analyze API enables us to combine various analyzers, tokenizers, token filters and other components of the analysis process together to test various query combinations and get immediate results.

#### Playing the analyze API
```shell
curl --silent --request POST 'http://localhost:9200/movies/_analyze?pretty' \
--data-raw '{
   "tokenizer" : "standard",
   "filter": [{"type":"edge_ngram", "min_gram": 1, "max_gram": 4}],
   "text" : "Star"
}'
```
```shell
curl --request PUT 'http://localhost:9200/autocomplete' \
-d '{
   "mappings": {
       "properties": {
           "title": {
               "type": "search_as_you_type"
           },
           "genre": {
               "type": "search_as_you_type"
           }
       }
   }
}'
```
```shell
curl --silent --request POST 'http://localhost:9200/_reindex?pretty' --data-raw '{
 "source": {
   "index": "movies"
 },
 "dest": {
   "index": "autocomplete"
 }
}' | grep "total\|created\|failures"
```
```shell
INPUT=''
```
```shell
curl -s --request GET 'http://localhost:9200/autocomplete/_search?pretty' --data-raw '{
   "size": 5,
   "query": {
       "multi_match": {
           "query": "Sta",
           "type": "bool_prefix",
           "fields": [
               "title",
               "title._2gram",
               "title._3gram"
           ]
       }
   }
}'
```
```shell

```
