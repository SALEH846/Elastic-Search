### Fuzzy Queries

#### Fuzzy Matches
- A way to acocunt for typos and misspellings.
- The *levenshtein edit distance* accounts for:
  - *Substitutions* of characters (interstellar -> intersteller)
  - *Insertions* of characters (interstellar -> insterstellar)
  - *Deletion* of characters (interstellar -> interstelar)
- All of the above have an edit distance of 1.

#### The Fuzziness Parameter
```shell
curl -XGET 127.0.0.1:9200/movies/_search?pretty -d'
{
  "query": {
    "fuzzy": {
      "title": {"value": "intrsteller", "fuzziness": 2}
    }
  }
}
'
```

#### Auto Fuzziness
- Fuzziness: AUTO
  - 0 for 1-2 character strings
  - 1 for 3-5 character strings
  - 2 for anything else
