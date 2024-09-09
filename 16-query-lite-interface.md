### Query-lite interface

- Search the movie index where title contains star
```
/movies/_search?q=title:star
```

```
/movies/_search?q=+year:>2010+title:trek
```

- Good for experimenting
- Should not be used in production
- Formally known as **"URI search"**. Search for that in ElasticSearch documentation