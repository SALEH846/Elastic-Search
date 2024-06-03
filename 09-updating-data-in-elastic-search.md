- Every document has a `_version` field.
- ElasticSearch documents are immutable.
- When you update an existing document:
    - a new document is created with an incremented _version
    - the old document is marked for deletion.

```shell
curl -XPUT 127.0.0.1:9200/movies/_doc/109487?pretty -d '
{
    "genre": ["IMAX", "Sci-Fi"],
    "title": "Interstellar foo",
    "year": 2014
}'
```
- After running the above command, the `_version` of the existing document is incremented, which you can verify by running the below given command.

```shell
curl -XGET 127.0.0.1:9200/movies/_doc/109487?pretty
```

```shell
curl -XPOST 127.0.0.1:9200/movies/_update/109487 -d '
{
    "doc": {
        "title": "Interstellar"
    }
}'
```
- Another way of updating the document.