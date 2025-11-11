``

# MongoDB Data Modeling

MongoDB is a **NoSQL, document-oriented database** that stores data in **BSON** (Binary JSON).
Unlike relational databases (SQL) where schema is structured and fixed, MongoDB provides a **flexible schema** design.

Effective MongoDB data modeling focuses on:

* Application access patterns
* Performance and scaling
* Query complexity vs. storage efficiency
* Balance between **embedding** and **referencing**

---

## Key Principles of MongoDB Data Modeling

1. **Model data according to application queries**
2. **Prefer embedding for data that is accessed together**
3. **Use referencing for large or shared data**
4. **Optimize for read-heavy or write-heavy patterns**
5. **Avoid unnecessary joins (lookups)**
6. **Use schema validation rules for consistency when needed**

---

## Schema Embedding (Denormalization)

### Definition

Store related data within a single document.

### Best for

* One-to-few relationships
* Data always read together
* Real-time reads with minimal joins
* Fast read performance

### Example: Blog With Embedding Comments

```js
db.posts.insertOne({
  _id: 1,
  title: "MongoDB Modeling",
  author: "Ravi",
  content: "Detailed guide...",
  comments: [
    { user: "Arun", comment: "Great post", date: "2025-10-01" },
    { user: "Neha", comment: "Helpful info", date: "2025-10-02" }
  ]
})
```

### Pros

* Very fast reads
* No extra queries (no `$lookup`)
* Ideal for caching and nested docs

### Cons

* Document growth can become large
* Hard to update individual items in large arrays
* Risk of document reaching MongoDB size limit (16MB)

---

## Schema Referencing (Normalization)

### Definition

Store related data in separate collections and **reference** by ID.

### Best for

* Many-to-many or one-to-many relationships
* Frequently updated data
* Shared data across collections
* Large subdocuments

### Example: Blog With Referenced Comments

**posts collection**

```js
{ _id: 1, title: "MongoDB Modeling", author: "Ravi" }
```

**comments collection**

```js
{ post_id: 1, user: "Arun", comment: "Great post", date: "2025-10-01" }
```

Query with `$lookup`:

```js
db.posts.aggregate([
  {
    $lookup: {
      from: "comments",
      localField: "_id",
      foreignField: "post_id",
      as: "comments"
    }
  }
])
```

### Pros

* Scales well for large data
* Easier updates on sub-data
* Prevents oversized docs

### Cons

* Requires joins (slower)
* More complex queries
* More round-trips if lookup not used

---

## Normalization & Denormalization in MongoDB

| Concept         | SQL Meaning              | MongoDB Approach |
| --------------- | ------------------------ | ---------------- |
| Normalization   | Minimize redundancy      | Referencing      |
| Denormalization | Duplicate data for speed | Embedding        |

---

### Normalization (Referencing)

Used when:

* Consistency is a priority
* Data duplication must be avoided
* Relationships are complex

Example: Store `user_id` in orders, refer to users collection

```js
db.orders.insertOne({
  order_id: 101,
  user_id: 7,
  total: 1200
})
```

---

### Denormalization (Embedding)

Used when:

* Speed is a priority
* Data frequently accessed together
* Write amplification is manageable

Example: store user info inside order

```js
db.orders.insertOne({
  order_id: 101,
  user: { id: 7, name: "Arun", phone: "9990001111" },
  total: 1200
})
```

---

## Choosing Embedding vs Referencing

| Criteria          | Embed         | Reference              |
| ----------------- | ------------- | ---------------------- |
| Data size         | Small         | Large                  |
| Read frequency    | Read together | Independent reads      |
| Update frequency  | Rare updates  | Frequent updates       |
| Relationship type | One-to-few    | One-to-many, Many-many |
| Atomicity         | Needed        | Not required           |
| Document growth   | Small         | Large                  |

---

## Schema Design Considerations

1. **Data access patterns**

    * Which fields queried most
    * Join frequency

2. **Document size**

    * MongoDB limit = 16 MB

3. **Read vs Write priority**

    * Read-heavy → embed
    * Write-heavy → reference

4. **Array size and growth**

    * Limit arrays; use pagination if large

5. **Indexing strategy**

    * Optimize for frequent queries
    * Use compound indexes correctly

6. **Sharding considerations**

    * Select shard key wisely
    * Avoid monotonically increasing shard keys

7. **Avoid unbounded arrays**

    * Use bucket pattern for large logs or events

Example of bucket pattern:

```js
{
  user_id: 1,
  month: "2025-01",
  login_logs: [
    { timestamp: "...", ip: "..."},
    ...
  ]
}
```

---

## Practical Example: E-commerce Data Modeling

### Products with embedded specifications

```js
{
  _id: 101,
  name: "Laptop",
  specs: { ram: "16GB", cpu: "i7", ssd: "512GB" }
}
```

### Order referencing users and items

```js
{
  order_id: 9001,
  user_id: 55,
  items: [
    { product_id: 101, qty: 1 },
    { product_id: 103, qty: 2 }
  ],
  address: { city: "Hyderabad", pincode: "500081" }
}
```

---

## Summary

| Concept         | Use When                               |
| --------------- | -------------------------------------- |
| Embedding       | Small related data, fast reads         |
| Referencing     | Large or shared data, frequent updates |
| Normalization   | Better consistency, avoid duplication  |
| Denormalization | Better performance, fewer joins        |
| Design focus    | Query patterns > Storage savings       |

---

## Interview Pointers

* MongoDB optimizes for read performance
* Embedding improves read speed but risks large documents
* `$lookup` is equivalent to SQL join
* Balance **atomicity vs scalability**
* Schema evolves around application usage
