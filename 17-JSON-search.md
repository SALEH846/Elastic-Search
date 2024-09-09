- Request body search
- This is the way you should perform queries in production
```shell
curl -XGET 127.0.0.1:9200/movies/_search?pretty -d '
{
    "query": {
        "match": {
            "title": "star"
        }
    }
}'
```

- Queries: return data in terms of relevance
- Filters: ask a yes/no question of your data
- Use filters when you can -- they are faster and cacheable

- Example: Boolean query with a filter
```shell
curl -XGET 127.0.0.1:9200/movies/_search?pretty -d '
{
    "query": {
        "bool": {
            "must": {"term": {"title": "trek"}},
            "filter": {"range": {"year": {"gte": 2010}}}
        }
    }
}'
```

- **Some types of filters**
- `term`: Filter by exact values
`{"term": {"year": 2014}}`
- `terms`: Match if any exact values in a list match
`{"terms": {"genre": ["Sci-Fi", "Adventure"]}}`
- `range`: Find numbers or dates in a given range (gt, gte, lt, lte)
`{"range": {"year": {"gte": 2010}}}`
- `exists`: Find documents where a field exists
`{"exists": {"field": "tags"}}`
- `missing`: Find documents where a field is missing
`{"missing": {"field": "tags"}}`
- `bool`: Combine filters with Boolean logic (must, must_not, should)

- **Some types of filters**
- `Match_all`: Returns all documents and is the default. Normally used with a filter.
`{"match_all": {}}`
- `Match`: Searches analyzed results, such as full text search.
`{"match": {"title": "star"}}`
- `Multi_match`: Run the same query on multiple fields.
`{"multi_match": {"query": "star", "fields": ["title", "synopsis"]}}`
- `Bool`: Works like a bool filter, but results are scored by relevance.

- Queries are wrapped in a `"query": {}` block.
- Filters are wrapped in a `"filter": {}` block.
- You can combine filters inside queries, or queries inside filters too.
