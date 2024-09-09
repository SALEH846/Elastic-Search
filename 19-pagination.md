### Pagination

- **Specify "From" and "Size"**
- Pagination Syntax
```shell
curl -XGET 127.0.0.1:9200/movies/_search?size=2&from=2&pretty

curl -XGET 127.0.0.1:9200/movies/_search?pretty -d'
{
  "from": 2,
  "size": 2,
  "query": {"match": {"genre": "Sci-Fi"}}
}
'
```
- Beware:
  - Deep pagination can kill performance.
  - Every result must be retrieved, collected and sorted.
  - Enforce an upper bound on how many results you'll return to users.
