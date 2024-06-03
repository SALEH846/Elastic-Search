- Get data
    - `wget http://media.sundog-soft.com/es8/movies.json`


```shell
curl -XPUT 127.0.0.1:9200/_bulk?pretty --data-binary @movies.json
```
- `movies.json` is a file name in this case which contains data.
