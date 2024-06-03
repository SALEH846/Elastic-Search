Import a single movie
```shell
curl -H "Content-Type: application/json" -XPUT 127.0.0.1:9200/movie -d '
{
    "mappings": {
        "properties": {
            "year": {
                "type": "date"
            }
        }
    }
}'
```

GET request on the mapping to see that information is actually there
```shell
curl -XGET 127.0.0.1:9200/movies/_mapping
```

Insert a movie into elasticsearch index
```shell
curl -H "Content-Type: application/json" -XPUT 127.0.0.1:9200/movies/_doc/109487?pretty -d '
{
    "genre": ["IMAX", "Sci-Fi"],
    "title": "Interstellar",
    "year": 2014
}'
```

Search for the inserted movie
```shell
curl -XGET 127.0.0.1:9200/movies/_search?pretty
```