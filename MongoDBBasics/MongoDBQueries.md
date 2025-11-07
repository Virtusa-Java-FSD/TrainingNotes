## Introduction to MongoDB

**MongoDB** is a NoSQL database that uses a document-oriented data model. It is designed for high performance, high availability, and easy scalability. MongoDB is known for its flexibility in handling various types of data and its capability to manage large volumes of data efficiently. It is an open-source database developed by MongoDB, Inc.

## Architecture and Concepts

### MongoDB Architecture

MongoDB follows a distributed architecture with the following key components:

1\. **Database**: A physical container for collections.

2\. **Collection**: A group of MongoDB documents, equivalent to tables in relational databases.

3\. **Document**: A set of key-value pairs, the basic unit of data in MongoDB, equivalent to rows in relational databases.

4\. **Shard**: A single instance of MongoDB that holds a subset of the data. Sharding allows MongoDB to horizontally partition data across multiple servers.

5\. **Replica Set**: A group of MongoDB servers that maintain the same data set, providing redundancy and high availability.

### Key Concepts

1\. **Document-Oriented Storage**: Data is stored in flexible, JSON-like documents.

2\. **Dynamic Schema**: No need to predefine the schema, making it easier to store varied data formats.

3\. **Indexing**: Supports indexing on any field within the document.

4\. **Replication**: Ensures high availability through replica sets.

5\. **Sharding**: Distributes data across multiple servers for scalability.

## Document Data Model

The **document data model** in MongoDB stores data in BSON (Binary JSON) format, which allows for nested structures and arrays. This flexibility enables the storage of complex data structures and varied data formats without the need for rigid schemas.

### Example of a Document

```json

{

    "_id": ObjectId("507f1f77bcf86cd799439011"),

    "name": "Alice",

    "age": 30,

    "address": {

        "street": "123 Main St",

        "city": "Anytown",

        "state": "CA",

        "zip": "12345"

    },

    "hobbies": ["reading", "cycling", "hiking"]

}

```

## Collections and Databases

### Collections

A **collection** in MongoDB is a group of documents. Collections are similar to tables in relational databases but are schema-less, meaning documents within a collection can have different fields.

### Databases

A **database** is a container for collections. Each database can have its own set of collections and is isolated from other databases.

### Example

Creating a collection and inserting a document:

```javascript

use myDatabase;

db.createCollection("myCollection");

db.myCollection.insertOne({

    "name": "Alice",

    "age": 30,

    "hobbies": ["reading", "cycling"]

});

```

## BSON (Binary JSON)

**BSON (Binary JSON)** is a binary representation of JSON-like documents. BSON extends JSON with additional data types and is designed to be efficient in both storage and scanning speed. It supports data types such as integers, floats, dates, and binary data, which are not natively supported by JSON.

### Example of BSON

A JSON document like:

```json

{

    "name": "Alice",

    "age": 30,

    "active": true

}

```

might be represented in BSON as a sequence of bytes that efficiently encodes the field names and values.

## ObjectId

**ObjectId** is the default primary key for MongoDB documents. It is a 12-byte identifier that ensures uniqueness across a distributed system. The ObjectId is composed of:

1\. **4-byte timestamp**: Represents the seconds since the Unix epoch.

2\. **5-byte random value**: Ensures uniqueness within a second.

3\. **3-byte incrementing counter**: Ensures uniqueness within a single second.

### Example of an ObjectId

```javascript

ObjectId("507f1f77bcf86cd799439011")

```

This ObjectId can be broken down as follows:

- Timestamp: 507f1f77 (seconds since epoch)

- Random value: bcf86cd7

- Counter: 99439011

### Usage of ObjectId

When inserting a document without an `_id` field, MongoDB will automatically generate an `ObjectId`.

```javascript

db.myCollection.insertOne({

    "name": "Alice",

    "age": 30

});

// MongoDB will generate an ObjectId for the _id field

```

### Querying by ObjectId## Connecting to MongoDB

### Connecting to MongoDB using MongoDB Shell

To connect to a MongoDB database using the MongoDB shell, you can use the `mongo` command followed by the database server's address. By default, MongoDB listens on port 27017.

```sh

mongo --host localhost --port 27017

```

You can also connect to a specific database:

```sh

mongo --host localhost --port 27017 myDatabase

```


## Basic CRUD (Create, Read, Update, Delete) Operations

### Create

To insert documents into a collection, you can use the `insertOne` or `insertMany` methods.

```javascript

db.users.insertOne({ name: "Alice", age: 30, email: "alice@example.com" });

db.users.insertMany([

    { name: "Bob", age: 25, email: "bob@example.com" },

    { name: "Carol", age: 35, email: "carol@example.com" }

]);

```

### Read

To query documents from a collection, you can use the `find` method.

```javascript

// Find all documents

db.users.find();

// Find documents with a specific condition

db.users.find({ age: { $gte: 30 } });

```

### Update

To update documents in a collection, you can use the `updateOne` or `updateMany` methods.

```javascript

// Update a single document

db.users.updateOne(

    { name: "Alice" }, // Filter

    { $set: { age: 31 } } // Update operation

);

// Update multiple documents

db.users.updateMany(

    { age: { $lt: 30 } }, // Filter

    { $set: { status: "active" } } // Update operation

);

```

### Delete

To delete documents from a collection, you can use the `deleteOne` or `deleteMany` methods.

```javascript

// Delete a single document

db.users.deleteOne({ name: "Alice" });

// Delete multiple documents

db.users.deleteMany({ age: { $lt: 30 } });

```

## Query Syntax and Operators

### Basic Query Syntax

MongoDB queries are written in JSON-like format, specifying the conditions for selecting documents.

```javascript

db.collection.find({ <field>: <value> });

```

### Comparison Operators

- `$eq`: Matches values that are equal to a specified value.

- `$ne`: Matches values that are not equal to a specified value.

- `$gt`: Matches values that are greater than a specified value.

- `$gte`: Matches values that are greater than or equal to a specified value.

- `$lt`: Matches values that are less than a specified value.

- `$lte`: Matches values that are less than or equal to a specified value.

```javascript

db.users.find({ age: { $gte: 30 } });

```

### Logical Operators

- `$and`: Joins query clauses with a logical AND, returns all documents that match the conditions.

- `$or`: Joins query clauses with a logical OR, returns all documents that match any of the conditions.

- `$not`: Inverts the effect of a query expression.

- `$nor`: Joins query clauses with a logical NOR, returns all documents that fail to match both clauses.

```javascript

db.users.find({

    $and: [

        { age: { $gte: 30 } },

        { status: "active" }

    ]

});

db.users.find({

    $or: [

        { name: "Alice" },

        { name: "Bob" }

    ]

});

```

### Element Operators

- `$exists`: Matches documents that have the specified field.

- `$type`: Matches documents with a specified BSON type.

```javascript

db.users.find({ email: { $exists: true } });

db.users.find({ age: { $type: "int" } });

```

### Array Operators

- `$in`: Matches any of the values specified in an array.

- `$nin`: Matches none of the values specified in an array.

- `$all`: Matches arrays that contain all elements specified in an array.

- `$size`: Matches arrays with the specified number of elements.

```javascript

db.users.find({ name: { $in: ["Alice", "Bob"] } });

db.users.find({ hobbies: { $all: ["reading", "cycling"] } });

db.users.find({ hobbies: { $size: 2 } });

```

### Update Operators

- `$set`: Sets the value of a field.

- `$unset`: Removes the specified field from a document.

- `$inc`: Increments the value of a field by a specified amount.

- `$push`: Adds an item to an array.

- `$pull`: Removes an item from an array.

```javascript

db.users.updateOne(

    { name: "Alice" },

    { $set: { age: 31 } }

);

db.users.updateOne(

    { name: "Bob" },

    { $inc: { age: 1 } }

);

db.users.updateOne(

    { name: "Carol" },

    { $push: { hobbies: "swimming" } }

);

db.users.updateOne(

    { name: "Carol" },

    { $pull: { hobbies: "cycling" } }

);

```

MongoDB provides a rich set of operators and functions to interact with the database, making it highly versatile for various types of applications.

You can query documents using the ObjectId:

```javascript

var id = ObjectId("507f1f77bcf86cd799439011");

db.myCollection.findOne({ "_id": id });

```

MongoDB's flexible schema, document-oriented data model, and powerful features like sharding and replication make it a suitable choice for a wide range of applications, from small startups to large enterprise systems.