

# MongoDB: Projection, Sort, Skip, and Limit

In MongoDB, after querying documents, we often want to:

* Select only required fields (Projection)
* Order results (Sort)
* Retrieve a subset of results (Skip and Limit)

These operations help optimize performance, minimize data transfer, and structure the output effectively.

---

## Sample Data

For demonstration, consider the following collection:

```js
db.students.insertMany([
  { name: "Amit", age: 22, score: 88, branch: "CSE" },
  { name: "Riya", age: 21, score: 92, branch: "ECE" },
  { name: "Kunal", age: 23, score: 78, branch: "IT" },
  { name: "Sara", age: 22, score: 85, branch: "CSE" },
  { name: "Arjun", age: 20, score: 95, branch: "ECE" }
])
```

---

## 1. Projection

Projection is used to include or exclude fields from the query result.

### Syntax

```js
db.collection.find(query, projection)
```

### Example: Select only name and score

```js
db.students.find({}, { name: 1, score: 1, _id: 0 })
```

Explanation:

* `1` means include
* `0` means exclude
* `_id` is included by default, so we explicitly exclude it

### Exclude fields example

```js
db.students.find({}, { age: 0, branch: 0 })
```

---

### Mixed include and exclude rule

You cannot mix inclusion and exclusion in projection, except for `_id`.

Valid:

```js
{ name: 1, score: 1, _id: 0 }
```

Invalid:

```js
{ name: 1, age: 0 }
```

Exception: `_id` can be explicitly excluded or included without conflict.

---

## 2. Sort

Sorting arranges documents in ascending or descending order.

### Syntax

```js
db.collection.find().sort({ field: 1 or -1 })
```

* `1` = ascending
* `-1` = descending

### Example: Sort students by score descending

```js
db.students.find().sort({ score: -1 })
```

### Example: Sort by branch ascending, then score descending

```js
db.students.find().sort({ branch: 1, score: -1 })
```

---

## 3. Limit

Limit restricts the number of documents returned.

### Syntax

```js
db.collection.find().limit(n)
```

### Example: Return top 2 students by score

```js
db.students.find().sort({ score: -1 }).limit(2)
```

---

## 4. Skip

Skip omits a specified number of documents from the result.

### Syntax

```js
db.collection.find().skip(n)
```

### Example: Skip first 2 students and return the rest

```js
db.students.find().skip(2)
```

---

## 5. Combining Sort, Skip, Limit (Pagination)

Pagination is achieved using sort, skip, and limit together.

### Example: Page-wise data retrieval

Let page size = 2
Fetch page 2 (index starts at 0)

Formula:

```
skip = page_number * page_size
```

Query:

```js
db.students.find()
  .sort({ score: -1 })
  .skip(2)
  .limit(2)
```

This returns 3rd and 4th highest scoring students.

---

## Practical Use Cases

| Requirement                   | Operation           |
| ----------------------------- | ------------------- |
| Retrieve only required fields | Projection          |
| Sort data by rank/score/date  | Sort                |
| Implement pagination          | Sort + Skip + Limit |
| Reduce response payload       | Projection + Limit  |
| User leaderboard              | Sort + Limit        |
| Search results pages          | Skip + Limit        |

---

## Best Practices

1. Always define sort before skip and limit
2. Use indexes on sorted fields for performance
3. Limit result sets to avoid heavy cursor memory usage
4. Avoid skipping very large numbers in high-scale collections (prefer range-based pagination instead)

---

## Summary

| Feature    | Purpose        | Example               |
| ---------- | -------------- | --------------------- |
| Projection | Select fields  | `{ name: 1, _id: 0 }` |
| Sort       | Order docs     | `{ score: -1 }`       |
| Limit      | Restrict count | `.limit(5)`           |
| Skip       | Skip docs      | `.skip(10)`           |

---
