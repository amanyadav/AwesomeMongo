## How mongo drivers work

All MongoDB drivers perform three major functions :

1. Generate MongoDB object IDs. These are the default values stored in the _id field of all documents. 
2. Next, the drivers convert any language-specific representation of documents to and from BSON, the binary data format used by MongoDB.
3. Finally it communicate with the database over a TCP socket using the MongoDB wire protocol. But the style of socket communication, in particular whether writes on the socket wait for a response, is important, and we’ll explore the topic in this section.

### Databases
A database is a namespace and physical grouping of collections and their indexes.

### Collections
Collections are containers for structurally or conceptually similar documents. 

#### Create Collection
```
db.createCollection("users")
```

```
db.createCollection("users", {size: 20000})
```
collection with pre allocated size

### Rename Collection
```
db.products.renameCollection("store_products")
```

## Capped Collection
Once a capped collection reaches its maximum size, subsequent inserts will overwrite the least-recently-inserted documents in the collection. Generally used for logging purpose.
```
db.createCollection("users.actions", {capped: true, size: 16384, max: 100})
```
Create capped collection with fixed size and no. of documents. Size always precedes max records variable. 
1. You can’t delete individual documents from a capped collection
2. You can't perform any update that will increase the size of a document. 
Capped collections were originally designed for logging, so there was no need to implement the deletion or updating of documents.

## TTL COLLECTIONS
MongoDB also allows you to expire documents from a collection after a certain amount of time has passed.
```
db.reviews.createIndex({time_field: 1}, {expireAfterSeconds: 3600})
```
1. You can’t have a TTL index on _id, or on a field used in another index. 
2. You also can’t use TTL indexes with capped collections because they don’t support removing individual documents. 
3. You can’t have compound TTL indexes, though you can have an array of timestamps in the indexed field. In that case, the TTL property will be applied to the earliest timestamp in the collection.

## LIMITS ON DOCUMENTS
1. BSON documents in MongoDB v2.0 and later are limited to 16 MB in size.11 The limit exists for two related reasons. First, it’s there to prevent developers from creating ungainly data models. Though poor data models are still possible with this limit, the 16 MB limit helps discourage schemas with oversized documents. If you find yourself needing to store documents greater than 16 MB, consider whether your schema should split data into smaller documents, or whether a MongoDB document is even the right place to store such information—it may be better managed as a file.
2. The second reason for the 16 MB limit is performance-related. On the server side, querying a large document requires that the document be copied into a buffer before being sent to the client. This copying can get expensive, especially (as is often the case) when the client doesn’t need the entire document.12 In addition, once sent, there’s the work of transporting the document across the network and then deserializ- ing it on the driver side. This can become especially costly if large batches of multi- megabyte documents are being requested at once.