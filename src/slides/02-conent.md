
## Why NoSQL

- Handleing large amounts of data
- Schema management is optional
  
---

## Why MongoDB

- Mature (v4.x)
- Can handle most data persistance usecases
- High indexing abilitites
- Extensive aggregation framework
- Transaction support

---

## Data Models

Flexible Schema

---

## Document Structure

- Embedded Data
- References

---
## Atomicity of Write Operations

---

## Getting started

Install Mongdb and client

[MongoDB Community Edition](https://www.mongodb.com/download-center/community)

[Robo - 3T](https://robomongo.org/download)

---

## Creating a DB, Collection & Document

1. start the server 

```sh
# on mack

/path/to/mongodb-macos-x86_64-4.2.2/bin/mongod --dbpath ./mongo-data/

```

```sh
# On windows

C:\path\to\mongo\bin\mongod.exe --dbpath C:\mongo-data
```



---

## My first query

`insert`, `insertMany`

`find({})`, `findOne({})`

`remove({})`

`update()`, `updateMany()`

---

## NodeJS Scripts

```js
const MongoClient = require('mongodb').MongoClient;

(async () => {
    const client = new MongoClient(config.MONGODB_GLOBAL_URI, {
        useNewUrlParser: true,
    });
    try {
        await client.connect();
        const db = client.db('[DB name]');
        const collection = db.collection('[collection name]');
        // ...
    }catch(err) {
        console.log(err);
    }
})()
```

---

# Find: `collection.find(query, projection)`

---

## Query - the selection filter

```js
db.users.findOne({ _id: '12345' })

db.users.find({ gender: 'male', age: 20 })
```
---

### Comparison operators

|Name|Description|
|-|-|
|$eq|Matches values that are equal to a specified value.|
|$gt|Matches values that are greater than a specified value.|
|$gte|Matches values that are greater than or equal to a specified value.|
|$in|Matches any of the values specified in an array.|
|$lt|Matches values that are less than a specified value.|
|$lte|Matches values that are less than or equal to a specified value.|
|$ne|Matches all values that are not equal to a specified value.
|$nin|Matches none of the values specified in an array.

---

```js
db.users.find({ color: { $in: ['red', 'green', 'blue']}})

db.users.find({ age: { $gt: 20 }})
```

---

## Logical

|Name|Description|
|-|-|
$and|Joins query clauses with a logical AND returns all documents that match the conditions of both clauses.
$not|Inverts the effect of a query expression and returns documents that do not match the query expression.
$nor|Joins query clauses with a logical NOR returns all documents that fail to match both clauses.
$or|Joins query clauses with a logical OR returns all documents that match the conditions of either clause.

```js
db.users.find({ $or: [{ name: 'John' }, { name: 'Snow' }])
```

---

## Element

|Name|Description|
|-|-|
$exists|Matches documents that have the specified field.
$type|Selects documents if a field is of the specified type.

```js
db.users.find({ email: { $exists: 1 }})
```

---

See full documentations [here](https://docs.mongodb.com/manual/reference/operator/query/)

---

## Sort

```js
db.collection.find({...}).sort( { age: -1 } )
```

---

## Limit

```js
db.collection.find({...}).limit(10)
```

---

## Count

```js
db.collection.find({...}).count()

// or

db.collection.count({...})
```

---

Lets get some data:

[AirBnB Data](http://insideairbnb.com/get-the-data.html)

---

## Practice

1. Find all the reviews by 'Jean'
2. Find all the reviews by 'Jean' that begin with the word 'Great'
3. List last 10 reviews by 'Jean' 
4. Find all the listings in 'Brooklyn'
5. Find all the listings with 4 bedrooms and at least 2 bathrooms
6. Find the cheapest 10 listings of 5 bedroom in 'Brooklyn'
7. Find the most expensive listing in 'Manhattan'

---
## Advanced Practice

1. Find all listings that 'Jean' has reviewd
2. Update the price of listing (`_id: '2595'`) to 555
3. Add a review to a listing (`_id: '2595'`)
4. Increase all prices for listings in Brooklyn by 10%
   
---

```js
// 1. Find all listings that 'Jean' has reviewd
db.getCollection('listings').find({ reviews: { $elemMatch: {reviewer_name: 'Jean'} }})

// 2. Update the price of listing (`_id: '2595'`) to 555
db.getCollection('listings').updateOne({ _id: '2595'}, {
    $set: { price: 555 }
})

// 2. Add a review to listing _id: '2595' 
db.getCollection('listings').updateOne({ _id: '2595'}, {
    $push: {
        reviews: {
            "_id" : "my_review",
            "listing_id" : "2595",
            "date" : "2020-02-22",
            "reviewer_id" : "007",
            "reviewer_name" : "Assaf",
            "comments" : "This is demo review"
        }
    }
})

// 4. Increase all prices for listings in Brooklyn by 10%
db.getCollection('listings').find({ city: 'Brooklyn' }).forEach(function(data) {
    db.listings.update({
        "_id": data._id,
    }, {
        "$set": {
            "price": data.price * 1.1
        }
    });
});
```

---

## Index

- Create Indexes to Support Your Queries
- Use Indexes to Sort Query Results

---

![Basic index](./index-for-sort.bakedsvg.svg)

---

## Creating an index

```js
db.collection.createIndex( <key and index type specification>, <options> )

```

```js
db.collection.createIndex( { name: -1 } )
```

---
## Index Types

1. Single field `{ score: 1 }`
2. Compound Index - `{ userid: 1, score: -1 }`
3. Multy-key index
4. Text Index
5. Wildcard Index `$**`
6. 2dsphere Index

---

# Aggrgation

---

## Aggregation Pipeline

- Uses a data pipeline process concept
- Each stage performs an operation or transformation on the data
- By default Mongo will scan the entire collection operating on a single document at a time
- When possible, place `$match` operators at the beginning of the pipeline

---

## Examples

[Zip Code Example](https://docs.mongodb.com/manual/tutorial/aggregation-zip-code-data-set/)

[User data](https://docs.mongodb.com/manual/tutorial/aggregation-with-user-preference-data/)

---
## Advanced queries

1. Find the listings with the most reviews
2. Find the City with the most listings
3. Find the City with the most listings of 4 bedrooms or more

---

## Solution 1

Option1
```js
db.getCollection('reviews').aggregate([
    { $group: { _id: "$listing_id", reviews: { $sum: 1 } } },
    { $sort: { "reviews": -1 } }
])
```

Option2
```js
db.getCollection('listings').aggregate([
   {
      $project: {
         name: 1,
         numberOfReviews: { $cond: { if: { $isArray: "$reviews" }, then: { $size: "$reviews" }, else: 0} }
      }
   },
   {
       $sort: {numberOfReviews: -1 }
   }
] )

```

---

## Solution 2

```js
db.getCollection('listings').aggregate([
    { $group: { _id: "$city", num_of_listings: { $sum: 1}}},
    { $sort: { "num_of_listings": -1 }}
])
```

---

## Solution 3

```js
db.getCollection('listings').aggregate([
    { $match: { bedrooms: { $gte: 4 }}},
    { $group: { _id: "$city", num_of_listings: { $sum: 1}}},
    { $sort: { "num_of_listings": -1 }}
])
```