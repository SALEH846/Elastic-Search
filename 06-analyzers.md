### What is a mapping?
- A mapping is a schema definition. Elasticsearch has reasonable defaults, but sometimes you need to customize them.

### Common mappings
- **Field types**
    - String, byte, short, integer, long, float, double, boolean, date
    ```
    "properties": {
        "user_id": {
            "type": "long"
        }
    }
    ```
- **Field index**
    - Do you want this field indexed for full-text search? analyzed / not_analyzed / no
    ```
    "properties": {
        "genre": {
            "index": "not_analyzed"
        }
    }
    ```
- **Filed Analyzer**
    - Define you tokenizer and token filter. standard / whitespace / simple / english etc.
    ```
    "properties": {
        "description": {
            "analyzer": "english"
        }
    }
    ```

### More about analyzers
- **Character filters**
    - Remove HTML encoding, convert `&` to `and`
- **Tokenizer**
    - Split strings in whitespace / punctuation / non-letters
- **Token Filter**
    - Lowercasing, stemming, synonyms, stopwords

### Choices for analyzers
- **Standard**
    - Splits on word boundaries, removes punctuation, lowercases, good choice if language is unknown
- **Simple**
    - Splits on anything that isn't a letter, and lowercase
- **Whitespace**
    - Splits on whitespace but doesn't lowercase
- **Language** (i.e. english)
    - Accounts for language-specific stopwords and stemming