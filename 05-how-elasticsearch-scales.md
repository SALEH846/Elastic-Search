- An index is split into shards.
- Documents are hashed to a particular shard.
- Each shard may be on a different node in a cluster.
- Every shard is a self-contained Lucene index of its own.

### Primary and replica shards
- Write requests are routed to the primary shard, then replicated.
- Read requests are routed to the primary shard or any replica.

- You can add more replica shards for more read throughput.
- The number shards can be set up front via a PUT command via REST/HTTP
    ```
    PUT /testindex
    {
        "settings": {
            "number_of_shards": 3,
            "number_of_replicas": 1
        }
    }
    ```