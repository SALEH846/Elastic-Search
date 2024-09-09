### Query Time Search As You Type

#### Abusing Sloppiness
```shell
curl -XGET '127.0.0.1:9200/movies/_search?pretty' -d'
{
  "query": {
    "match_phrase_prefix": {
      "title": {
          "query": "star trek",
          "slop": 10
      }
    }
  }
}
'
```
