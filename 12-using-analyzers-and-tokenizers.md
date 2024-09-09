- You need to make a decision every time you have a field that contains textual information. You can define it one of two types which determines that whether or not your text fields are searched as an exact match or if they are analyzed meaning that they can be partial matched ranked by relevance.
- If you want your text fields should be exact match only
    - Use keyword type when you are defining mapping for your index and that will suppress analyzing for that field completely.
    - It will only allow exact matches on keyword field.
    - Case-sensitive
- If you want partial matches
    - Use text type matching
    - Allow analyzers to run on that field
    - You can specify what analyzer to run on that field
    - There are different analyzers for specific languages
    - Depending on the analyzer, results will be case-insensitive, stemmed, stopwords removed, synonyms applied, etc.
    - Searches with multiple terms need not match them all

- Partial search query
```shell
curl -XGET 127.0.0.1:9200/movies/_search?pretty -d '
{
    "query": {
        "match": {
            "title": "Star Trek"
        }
    }
}'
```

```shell
curl -XGET 127.0.0.1:9200/movies/_search?pretty -d '
{
    "query": {
        "match_phrase": {
            "genre": "sci"
        }
    }
}'
```

- Delete a mapping
```shell
curl -XDELETE 127.0.0.1/9200/movies
```

- Create a new mapping
- `genre` has type equals `keyword` which means exact match
- `title` has type equals `text` which means partial matching and also has `analyzer` defined
```shell
curl -XPUT 127.0.0.1:9200/movies -d '
{
    "mappings": {
        "properties": {
            "id": {"type": "integer"},
            "year": {"type": "date"},
            "genre": {"type": "keyword"},
            "title": {"type": "text", "analyzer": "english"}
        }
    }
}'
```

- Load bulk data into the index
```shell
curl 127.0.0.1:9200/_bulk?pretty --data-binary @movies.json
```