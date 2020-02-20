
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
3. List top 10 reviews 

