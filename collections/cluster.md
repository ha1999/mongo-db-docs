# Clustered collections

> Starting in MongoDB 5.3, you can create a collection with a clustered index. Collections created with a clustered index are called clustered collections.

## 1. Benefits

> Because clustered collections store documents ordered by the clustered index key value, clustered collections have the following benefits compared to non-clustered collections:

- Faster queries on clustered collections without needing a secondary index, such as queries with range scans and equality comparisons on the clustered index key.

- Clustered collections have a lower storage size, which improves performance for queries and bulk inserts.

- Clustered collections can eliminate the need for a secondary TTL (Time To Live) index.

  - A clustered index is also a TTL index if you specify the expireAfterSeconds field.

  - To be used as a TTL index, the \_id field must be a supported date type. See TTL Indexes.

  - If you use a clustered index as a TTL index, it improves document delete performance and reduces the clustered collection storage size.

- Clustered collections have additional performance improvements for inserts, updates, deletes, and queries.

  - All collections have an \_id index.

  - A non-clustered collection stores the \_id index separately from the documents. This requires two writes for inserts, updates, and deletes, and two reads for queries.

  - A clustered collection stores the index and the documents together in \_id value order. This requires one write for inserts, updates, and deletes, and one read for queries.
