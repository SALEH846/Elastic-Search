### Sorting

#### Sorting Your Results Is Usually Quite Simple
- until the field you are sorting by is of data type integer or float.
```shell
curl -XGET 127.0.0.1:9200/movies/_search?sort=year&pretty
```
#### Unless You're Dealing With Strings
- A string field that is analyzed for full-text search can't be used to sort documents.
- This is because it exists in the inverted index as individual terms, not as the entire string.

#### If You Need To Sort On An Analyzed Text Field, Map A Keyword Copy
```shell
curl -XPUT 127.0.0.1:9200/movies/ -d'
{
  "mappings": {
    "properties": {
      "title": {
        "type": "text",
        "fields": {
          "raw": {
            "type": "keyword"
          }
        }
      }
    }
  }
}
'
```
- What we end up creating here is a title field that can be used for full text search and a title.raw field that you can use for sorting purposes or any other purpose that might require an unanalyzed field.

#### Now You Can Sort On The Keyword "Raw" Field
```shell
curl -XGET '12.0.0.1:9200/movies/_search?sort=title.raw?pretty'
```
- Sadly, you cannot change the mapping on an existing index.
- You'd have to delete the index, set up a new mapping, and re-index it.
- Like the number shards, this is something you should think about before importing data into your index.

#### NOTES
- To delete the index
```shell
curl -XDELETE 127.0.0.1:9200/movies
```
- Then, run the above given command to add title.raw field.
- Recreate the index
```shell
curl -XPUT 127.0.0.1:9200/_bulk --data-binary @movies.json
```
- Now, sort using `title.raw` field
```shell
curl -XGET '127.0.0.1:9200/movies/_search?sort=title.raw&pretty'
```
