

# MongoDB Aggregation Framework 

## 1. What is the Aggregation Framework?

The **Aggregation Framework** in MongoDB is a **powerful pipeline-based system** used to transform and analyze data. It performs operations similar to **SQL GROUP BY, JOIN, HAVING, SELECT, WHERE**, and analytical functions.

Aggregation pipeline = sequence of stages

```
[Stage1] → [Stage2] → [Stage3] → ... → Output
```

Example stages:

| Stage      | Purpose                                |
| ---------- | -------------------------------------- |
| $match     | Filter documents (like SQL WHERE)      |
| $project   | Select / reshape fields (like SELECT)  |
| $group     | Aggregate data (SUM, AVG, COUNT, etc.) |
| $sort      | Sort results                           |
| $limit     | Limit records                          |
| $skip      | Pagination                             |
| $unwind    | Explode array fields                   |
| $lookup    | Join collections                       |
| $addFields | Create new fields                      |
| $count     | Count documents                        |
| $facet     | Multiple pipelines in one query        |

---

## 2. Sample Collection (orders)

**orders collection**

```json
{
  "_id": 1,
  "orderId": "ORD001",
  "customer": "John Doe",
  "items": [
    { "product": "Laptop", "qty": 1, "price": 60000 },
    { "product": "Mouse", "qty": 2, "price": 800 }
  ],
  "orderDate": ISODate("2024-01-10"),
  "status": "Delivered",
  "city": "Chennai",
  "paymentMode": "UPI"
}
```

### Insert Sample Documents

```js
db.orders.insertMany([
{
  orderId: "ORD001",
  customer: "John Doe",
  items: [
    { product: "Laptop", qty: 1, price: 60000 },
    { product: "Mouse", qty: 2, price: 800 }
  ],
  orderDate: ISODate("2024-01-10"),
  status: "Delivered",
  city: "Chennai",
  paymentMode: "UPI"
},
{
  orderId: "ORD002",
  customer: "Priya",
  items: [
    { product: "Mobile", qty: 1, price: 25000 },
    { product: "Earphones", qty: 1, price: 2000 }
  ],
  orderDate: ISODate("2024-01-12"),
  status: "Delivered",
  city: "Bangalore",
  paymentMode: "Card"
},
{
  orderId: "ORD003",
  customer: "Rahul",
  items: [
    { product: "Laptop", qty: 1, price: 70000 }
  ],
  orderDate: ISODate("2024-01-15"),
  status: "Pending",
  city: "Chennai",
  paymentMode: "UPI"
}
]);
```

---

# 3. Aggregation Stages — Deep Dive

---

## A) `$match` — Filter Documents

### Theory

* Equivalent to SQL `WHERE`
* Filters early → improves performance
* Uses indexes if present

### Example: Find delivered orders

```js
db.orders.aggregate([
  { $match: { status: "Delivered" } }
]);
```

---

## B) `$project` — Select / Transform Fields

### Theory

* Include/exclude fields
* Create computed fields
* Rename fields
* Cannot filter rows (use $match for filtering)

### Example: Show orderId, customer, and calculated order value

```js
db.orders.aggregate([
  {
    $project: {
      orderId: 1,
      customer: 1,
      totalItems: { $size: "$items" }
    }
  }
]);
```

---

## C) `$unwind` — Expand Array Items

### Theory

* Converts array into multiple docs
* Needed before grouping on array elements

### Example: Expand items per order

```js
db.orders.aggregate([
  { $unwind: "$items" }
]);
```

---

## D) `$group` — Aggregate Data

### Theory

* Must specify `_id`
* Aggregation operators: `$sum`, `$avg`, `$max`, `$min`, `$push`, `$count`

### Example: Total orders per city

```js
db.orders.aggregate([
  { $group: { _id: "$city", totalOrders: { $sum: 1 } } }
]);
```

---

## E) `$sort` — Sort Results

### Example: Sort by latest orders

```js
db.orders.aggregate([
  { $sort: { orderDate: -1 } }
]);
```

---

## F) `$limit` and `$skip` — Pagination

### Example: Get second page (skip 1, limit 1)

```js
db.orders.aggregate([
  { $sort: { orderDate: -1 } },
  { $skip: 1 },
  { $limit: 1 }
]);
```

---

# 4. Multi-Stage Aggregation Examples

---

### Example 1: Calculate total bill per order

```
Order Value = sum(price * qty)
```

```js
db.orders.aggregate([
  { $unwind: "$items" },
  {
    $group: {
      _id: "$orderId",
      totalAmount: {
        $sum: { $multiply: ["$items.price", "$items.qty"] }
      }
    }
  }
]);
```

---

### Example 2: Total revenue by city

```js
db.orders.aggregate([
  { $unwind: "$items" },
  {
    $group: {
      _id: "$city",
      totalRevenue: { $sum: { $multiply: ["$items.price", "$items.qty"] }}
    }
  },
  { $sort: { totalRevenue: -1 } }
]);
```

---

### Example 3: Most frequently purchased product

```js
db.orders.aggregate([
  { $unwind: "$items" },
  {
    $group: {
      _id: "$items.product",
      qtySold: { $sum: "$items.qty" }
    }
  },
  { $sort: { qtySold: -1 } },
  { $limit: 1 }
]);
```

---

### Example 4: Count orders per payment mode, latest first

```js
db.orders.aggregate([
  { $group: { _id: "$paymentMode", total: { $sum: 1 } } },
  { $sort: { total: -1 } }
]);
```

---

# 5. Summary Table

| Stage        | Similar to SQL | Purpose                 |
| ------------ | -------------- | ----------------------- |
| $match       | WHERE          | Filter                  |
| $project     | SELECT         | Choose fields / compute |
| $unwind      | Explode array  | Normalize array data    |
| $group       | GROUP BY       | Aggregate               |
| $sort        | ORDER BY       | Sort results            |
| $skip/$limit | OFFSET/LIMIT   | Pagination              |

---

# 6. Practice Exercises

Try these:

| Task                               |
| ---------------------------------- |
| Show total revenue per customer    |
| Top 2 cities with highest sales    |
| Show orders that contain "Laptop"  |
| Find average order value           |
| List most common payment mode      |
| Show each customer's average spend |
