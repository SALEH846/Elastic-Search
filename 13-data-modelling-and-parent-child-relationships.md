- In a lot of distributed systems such as mongodb and cassandra, the usual adivice is to normalize your data.
- On the other hand, in relational databases different data records are tieds together using relations.
- In elasticsearch, you can model the data either way.

### Tradeoffs of normalizing and denormalizing data in elasticsearch
- Relational data -- normalized data
    - Let's say we have to show our user movies with particular rating, if we first look up movies with particular ratings then we get ratings as well as movie ids, so we have to make a second look up to get movie title based on movie ids.
    - This is a normalized approach.
    - Minimizes storage space, makes it easy to change titles.
    - But requires two queries, and storage is cheap!
    - Two queries will be made to the server instead of one.
- Non Relational data -- Denormalized
    - Data is duplicated, but only one query.
    - Updates are expensive -- as we have to update the title in each place.

- Elasticsearch has a built-in capability of modeling parent-child relationships.
- Create a new index
```shell
curl -XPUT 127.0.0.1:9200/series -d '
{
    "mappings": {
        "properties": {
            "film_to_franchise": {
                "type": "join",
                "relations": {"franchise": "film"}
            }
        }
    }
}'
```
- Get the data.
```shell
wget http://media.sundog-soft.com/es8/series.json
```
- Load data into the index using the bulk API
```shell
curl -XPUT 127.0.0.1:9200/_bulk?pretty --data-binary @series.json
```
- Let's query data for star wars franchise
```shell
curl -XGET 127.0.0.1:9200/series/_search?pretty -d '
{
    "query": {
        "has_parent": {
            "parent_type": "franchise",
            "query": {
                "match": {
                    "title": "Star Wars"
                }
            }
        }
    }
}'
```
- Find fracnchise associated with a prticular film.
```shell
curl -XGET 127.0.0.1:9200/series/_search?pretty -d '
{
    "query": {
        "has_child": {
            "type": "film",
            "query": {
                "match": {
                    "title": "The Force Awakens"
                }
            }
        }
    }
}'
```