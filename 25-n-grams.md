### N-Grams

#### Index-time With N-grams
- "star"
  - unigram: [s, t, a, r]
  - bigram: [st, ta, ar]
  - trigram: [sta, tar]
  - 4-gram: [star]
- Edge N-grams are built only on the beginning of each term.

#### Indexing N-Grams
1. Create an "Autocomplete" analyzer.
```shell
curl -XPUT '127.0.0.1:9200/movies?pretty' -d '
{
  "settings": {
    "analysis": {
      "filter": {
        "autocomplete_filter": {
          "type": "edge_ngram",
          "min_gram": 1,
          "max_gram": 20
        }
      },
      "analyzer": {
        "autocomplete": {
          "type": "custom",
          "tokenizer": "standard",
          "filter": [
            "lowercase",
            "autocomplete_filter"
          ]
        }
      }
    }
  }
}
'
```
2. Now map you field with it
```shell
curl -XPUT '127.0.0.1:9200/movies/_mapping?pretty' -d '
{
  "properties": {
    "title": {
      "type": "text",
      "analyzer": "autocomplete"
    }
  }
}
'
```
3. But only use N-grams on the index side
```shell
curl -XGET '127.0.0.1:9200/movies/_search?pretty' -d '
{
  "query": {
    "match": {
      "title": {
        "query": "sta",
        "analyzer": "standard"
      }
    }
  }
}
'
```
Otherwise our query will also get split into n-grams, and we'll get results for everything that matches 's', 't', 'a', 'st', etc.

#### Completion Suggesters
- You can also upload a list of all possible completions ahead of time using *completion suggesters*.

#### --------------------
1. Delete the current index
```shell
curl -XDELETE 127.0.0.1:9200/movies
```
2. Create a custom analyzer using the command given above in step 1.
3. Test the analyzer
```shell
curl -XGET '127.0.0.1:9200/movies/_analyze?pretty' -d '
{
  "analyzer": "autocomplete",
  "text": "sta"
}
'
```
4. Map this analyzer to out title field.
```shell
curl -XPUT '127.0.0.1:9200/movies/_mapping?pretty' -d '
{
  "properties": {
    "title": {
      "type": "text",
      "analyzer": "autocomplete"
    }
  }
}
'
```
5. Reindex your data
```shell
curl -XPUT 127.0.0.1:9200/_bulk?pretty --data-binary @movies.json
```
6. Test the newly created index
```shell
curl -XGET '127.0.0.1:9200/movies/_search?pretty' -d '
{
  "query": {
    "match": {
      "title": {
        "query": "sta"
      }
    }
  }
}
'
```
- NOTE: specify the analyzer of type standard on the query side
```shell
curl -XGET '127.0.0.1:9200/movies/_search?pretty' -d '
{
  "query": {
    "match": {
      "title": {
        "query": "sta",
        "analyzer": "standard"
      }
    }
  }
}
'
```
