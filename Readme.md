## Find queries
```
db.reviews.find({product_id:10 }).sort({helpful_votes: -1}).skip(0).limit(12)
```
skip mentioned pages. limit results and perform sort

```
db.users.find({'addresses.0.state': "NY"})
```
state in address array should at index 0 should be "NY"

```
db.users.find({'addresses.state': "NY"})
```
address array contains value of state key as "NY"

```
db.users.find({
    'addresses': {
        '$elemMatch': {
            'name': 'home',
            'state': 'NY'
        }
    }
})
```
use $elemMatch only when you need to match two or more attributes in a
subdocument.

```
db.users.find({'addresses': {$size: 3}})
```
query array by size

## regex
```
db.reviews.find({
            'user_id': ObjectId("4c4b1476238d3b4dd5000001"),
            'text': {
              '$regex': "best|worst",
              '$options': "i"}
          })
```
Using the case-insensitive flag has penalties; it excludes the use of any indexes, which in MongoDB are always case-sensitive.

## Projection
```
db.users.findOne({name:"aman"},{'_id': 1})
```
setting projection for id field only.

## Partial match
```
db.users.find({'last_name': /^Ba/})
```

### Querying range
```
db.users.find({'addresses.zip': {'$gt': 10019, '$lt': 10040}})
```

## Set operators
### In Operator {$in}
```
db.users.find({hobbies: {$in: ["dancing", "singing"]}})
```
return  users whose hobbies are either "dancing" or "singing". is present in provided list.

### All Operator {$all}
```
db.users.find({hobbies: {$all: ["dancing", "singing"]}})
```
if all attributes shown here are present in list.

### Not In Operator {$nin}
```
db.users.find({hobbies: {$nin: ["dancing", "singing"]}})
```
users whose noting match in this list will be returned.

## Boolean Operators
| Operator|Description|
| ------------- |-------------- |
| $ne | Matches if the argument is not equal to the element |
| $not | Inverts the result of a match |
| $or | Matches if any of the supplied set of query terms is true |
| $nor | Matches if none of the supplied set of query terms are true |
| $and | Matches if all of the supplied set of query terms are true|
| $exists | Matches if the element exists in the document.|

Use of $ne operator
```
db.products.find({'details.manufacturer': 'Acme', tags: {$ne: "gardening"} })
```

```
db.users.find({'age': {'$not': {'$lte': 30}}})
```
this query returns documents where age is greater than 30

```
db.products.find({
    '$or': [
      {'details.color': 'blue'},
      {'details.manufacturer': 'Acme'}
    ]
})
```

```
db.products.find({
    $and: [
      {
        tags: {$in: ['gift', 'holiday']}
}, {
        tags: {$in: ['gardening', 'landscaping']}
      }
] })
```

```
db.products.find({'details.color': {$exists: false}})
```
Exists operator.

### Adding data to Array
```
db.users.update(
    { "name": "Aman"},
    { $addToSet:
        { hobbies:"skating"}
    },
    false,
    true
)
```
1. query predicate
2. add to list using $addToSet operator
3. controls whether an upsert is allowed. This tells the update operation whether it should insert a document if it doesn’t already exist, which has different behavior depending on whether the update is an operator update or a replacement update.
4. indicates that this is a multi-update.

## Deleting data
#### Remove whole collection
```
db.foo.remove()
```

#### Remove selected documents
```
db.users.remove(
    { name : "Aman" }
)
```

#### Drop collection
```
db.users.drop()
```

## Range Query
```
db.users.find( 
    {
        age: {"$gt": 1991 }
    }
)
```
This will return users with age greater than 1991

```
db.users.find( {age: {"$gt": 1991, "$lt": 2016 }} )
```
Will return users with age greater then 1991 and less then 2016

## Creating Index
```
db.users.createIndex({uid: 1})
```
create index on users collection with __uid__ field. This lets you to fetch documents very fast when queried using uid field.

### unique index
```
db.products.createIndex({slug: 1}, {unique: true})
```
If you have a unique index on slug, an exception will be thrown if you try to insert a duplicate value.

```
db.products.ensureIndex({tags: 1})
```

```
db.users.getIndexes()
```
This query will return index information related to users collection.

## Explain
```
db.users.find({age: {"$gt": 19995}}).explain("executionStats")
```
When any database receives a query, it must plan out how to execute it, this is called a __query plan__. __explain__ describes query paths and allows developers to diagnose slow operations by determining which indexes a query has used.

## Administrative Commands
```
show dbs
```
show all the databases present on current mongo instance.

```
show collections
```
list out all the collections on current selected database

```
db.stats()
```
show status of database.

```
db.users.stats()
```
show status of users collection on selected database.