### Partial Matching

#### Prefix Queries On Strings
- If we revamped year to be a string...
```shell
curl -XGET '127.0.0.1:9200/movies/_search?pretty' -d'
{
  "query": {
    "prefix": {
      "year": "201"
    }
  }
}
'
```

#### Wildcard Queries
```shell
curl -XGET '127.0.0.1:9200/movies/_search?pretty' -d'
{
  "query": {
    "wildcard": {
      "year": "1*"
    }
  }
}
'
```
- "regexp" queries also exist
