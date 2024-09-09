### Another Filter Query Example

```shell
curl -XGET 127.0.0.1:9200/movies/_search?pretty -d'
{
  "query": {
    "bool": {
      "must": {"match": {"genre": "Sci-Fi"}},
      "must_not": {"match": {"title": "trek"}},
      "filter": {"range": {"year": {"gte": 2010, "lt": 2015}}}
    }
  }
}
'
```

#### Exercise: Using Filters
**Search for science fiction movies before 1960, sorted by title.**
```shell
curl -XGET '127.0.0.1:9200/movies/_search?sort=title.raw&pretty' -d'
{
  "query": {
    "bool": {
      "must": {"match": {"genre": "Sci-Fi"}},
      "filter": {"range": {"year": {"lt": 1960}}}
    }
  }
}
'
```
