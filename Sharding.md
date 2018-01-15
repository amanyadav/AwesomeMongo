## Definitions

### Chunk
Logical grouping of documents based on predetermined field or set of fields. This field is called __shard key__.

MongoDB’s sharding is range-based; this means that each “chunk” represents a range of shard keys.

If a query includes the shard key, then mongos can quickly consult the chunk data to determine exactly which shard contains the query’s result set. This is called a __targeted query__.

But if the shard key isn’t part of the query, the query planner will have to visit all shards to fulfill the query completely. This is known as a global or scatter/gather query.

Pitfall of choosing shard key:

1. __Hotspots__ Some shard keys result in situations where all reads or writes are going to a single chunk, on a single shard. This can cause one shard to be com- pletely overwhelmed, while the others are sitting idle.
2. __Unsplittable chunks__ A shard key that’s too coarse-grained can result in a situation where there are many documents with the same shard key. Because sharding is based on ranges of shard keys, this means that the documents can’t be split into separate chunks, which can limit MongoDB’s ability to evenly distribute data.
3. __Poor targeting__ Even if writes are distributed perfectly in the cluster, if our shard key doesn’t have some relationship to our queries, we’ll have poor query per- formance. You saw this in section 12.5.1 when we discussed global and tar- geted queries.