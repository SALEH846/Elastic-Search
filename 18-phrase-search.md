### Phrase Matching

- Must find all terms, in right order
```shell
curl -XGET 127.0.0.1:9200/movies/_search?pertty -d'
{
  "query": {
    "match_phrase": {
      "title": "star wars"
    }
  }
}
'
```

**Slop**
- Order matters, but you're OK with some words being in between the terms
```shell
curl -XGET 127.0.0.1:9200/movies/_search?pertty -d'
{
  "query": {
    "match_phrase": {
      "title": {"query": "star beyond", "slop": 1}
    }
  }
}
'
```
- The `slop` represents how far you're willing to let a term move to satisfy a phrase (in either direction!).
- Another example: "quick brown fox" would match "quick fox" with a slop of 1.
- If slop is 1, "star beyond" will match "beyond star"

**Proximity queries**
- Remember this is a query - results are sorted by relevance.
- Just use a really high slop if you want to get any documents that contain the words in your phrase, but want documents that have the words closer together scored higher.
```shell
curl -XGET 127.0.0.1:9200/movies/_search?pertty -d'
{
  "query": {
    "match_phrase": {
      "title": {"query": "star beyond", "slop": 100}
    }
  }
}
'
```

- Exercise: Search for "Star Wars" movies released after 1980, using both a `URI search` and a `request body search`.
```shell
curl -XGET 127.0.0.1:9200/movies/_search?pretty -d'
{
  "query": {
    "bool": {
      "must": {"match_phrase": {"title": "Star Wars"}},
      "filter": {"range": {"year": {"gt": 1980}}}
    }
  }
}
'
```
```shell
curl -XGET "127.0.0.1:9200/movies/_search?q=+year:>1980+title:star%20wars&pretty"
```
- Url encoded form
```shell
curl -XGET "127.0.0.1:9200/movies/_search?q=%2Byear%3A%3E1980+%2Btitle%3Astar%20wars&pretty"
```
