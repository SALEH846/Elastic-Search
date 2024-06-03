### Logical Concepts of Elasticsearch
- Documents
    - Documents are the things you're searching for. They can be more than text -- any structured JSON data works. Every document has a unique ID, and a type.
    - Can be thought of as like a row in the database.
- Indices
    - An index powers search into all documents within a collection of types. They contain **inverted indices** that let you search across everything within them at once, and **mappings** that define schemas for the data within.
    - Index is like a table in the database and the document is like row in it.
    - The schema that defines the data types of documents also belongs to the index.

- Elasticsearch works on top of JSON formatted data.
- Elasticsearch is built on top on RESTful inerface.
- You can have only one type of documents in an index.
- Index defines individual fields and what data types they are, that a document contains.