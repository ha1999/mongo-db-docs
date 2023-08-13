# Capped Collections

> Capped collections are fixed-size collections that support high-throughput operations that insert and retrieve documents based on insertion order. Capped collections work in a way similar to circular buffers: once a collection fills its allocated space, it makes room for new documents by overwriting the oldest documents in the collection.
>
> > **As an alternative to capped collections, consider MongoDB's TTL (Time To Live) indexes. As described in Expire Data from Collections by Setting TTL, these indexes allow you to expire and remove data from normal collections based on the value of a date-typed field and a TTL value for the index. TTL indexes are not compatible with capped collections**.

## 1. Behavior

- Insertion order
  > Capped collections guarantee preservation of the insertion order. As a result, queries do not need an index to return documents in insertion order. Without this indexing overhead, capped collections can support higher insertion throughput.
- Automatic removal of oldest documents
  > To make room for new documents, capped collections automatically remove the oldest documents in the collection without requiring scripts or explicit remove operations.

**Capped collections have an `_id` field and an index on the `_id` field by default.**

## 2. Restrictions and recommendations

- Reads

  > Starting in MongoDB 5.0, you cannot use read concern "snapshot" when reading from a capped collection.

- Updates

  > If you plan to update documents in a capped collection, create an index so that these update operations do not require a collection scan.

- Sharding
  > You cannot shard a capped collection.
- Query Efficiency

  > Use natural ordering to retrieve the most recently inserted elements from the collection efficiently. This is similar to using the tail command on a log file.

- Aggregation `$out`

  > The aggregation pipeline stage `$out` cannot write results to a capped collection.

- Transactions

  > Starting in MongoDB 4.2, you cannot write to capped collections in transactions.

- Stable API
  > Capped collections are not supported in Stable API V1.

## 3. Procedures

- Create a capped collection
  > You must create capped colllections explicitly using the `db.createCollection()` method, which is a `mongosh` helper for create command. When creating a capped collection you must specify the maximun `size of the collection in bytes`, which mongodb will pre-allocated for the collection. The size of capped collection includes a samll amount of space for interval overhead.
  ```python
  db.createCollection( "log", { capped: true, size: 100000 } )
  ```
- Additionally, you may also specify a maximum number of documents for the collection using the max field as in the following document:
  ```python
  db.createCollection("log", { capped : true, size : 5242880, max : 5000 } )
  ```

## 4. Operating on capped collection

- Query a capped collection
  > If you perform a find() on a capped collection with no ordering specified, MongoDB guarantees that the ordering of results is the same as the insertion order.
  > To retrieve documents in reverse insertion order, issue find() along with the sort() method with the $natural parameter set to -1, as shown in the following example:
  ```python
  db.cappedCollection.find().sort( { $natural: -1 } )    To retrieve documents in reverse insertion order, issue find() along with the sort() method with the $natural parameter set to -1, as shown in the following example:
  ```
- Check if a collection is capped collection
  ```python
  db.collection.isCapped()
  ```
- Convert a collection to capped
  ```python
  db.runCommand({"convertToCapped": "mycoll", size: 100000});
  ```
- Change the capped collection's size
  ```python
  db.runCommand( { collMod: "log", cappedSize: 100000 } )
  ```
- Change the maximum number of documents in a capped collection
  ```python
  db.runCommand( { collMod: "log", cappedMax: 500 } )
  ```