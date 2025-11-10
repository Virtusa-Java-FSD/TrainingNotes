
# MongoDB Regular Expressions 

MongoDB supports regular expressions (regex) to match string patterns in queries. Regex can be used inside the query filter to find documents where a string field matches a given pattern.

MongoDB uses **PCRE (Perl Compatible Regular Expressions)** engine.

---

## 1. Basic Regex Syntax in MongoDB

You can use regex in two ways:

### Method 1: Using `$regex` operator

```js
db.collection.find({ field: { $regex: "pattern" } })
```

### Method 2: Using native JS regex format

```js
db.collection.find({ field: /pattern/ })
```

---

## Sample Data

```js
db.users.insertMany([
 { name: "Alice Johnson", email: "alice@example.com" },
 { name: "Bob Marley", email: "bob123@example.org" },
 { name: "Charlie Rogers", email: "charlie@sample.com" },
 { name: "David Brown", email: "david99@test.com" },
 { name: "Evelyn Carter", email: "eve@test.org" }
])
```

---

## 2. Case-Insensitive Matching

### Example: Find users whose name contains “alice” ignoring case

```js
db.users.find({ name: { $regex: "alice", $options: "i" } })
```

### Native format

```js
db.users.find({ name: /alice/i })
```

> **`i`** = case-insensitive

---

## 3. Begins With (`^`)

### Find names starting with "D"

```js
db.users.find({ name: { $regex: "^D", $options: "i" } })
```

---

## 4. Ends With (`$`)

### Find emails ending in `.org`

```js
db.users.find({ email: { $regex: "\.org$", $options: "i" } })
```

---

## 5. Match Any Character (`.`)

### Names where second character is "a"

```js
db.users.find({ name: { $regex: "^.a" } })
```

Matches: **David**, **Charlie** (if Charlie matched conditions)

---

## 6. Match Digit `\d`

### Emails containing digits

```js
db.users.find({ email: { $regex: "\\d" } })
```

Result: **bob123**, **david99**

> Use double slashes `"\\d"` in JSON for escape

---

## 7. Character Class `[ ]`

### Find names starting with A, B, or C

```js
db.users.find({ name: { $regex: "^[ABC]", $options: "i" } })
```

---

## 8. Negation in Character Class `[^ ]`

### Names not starting with A-C

```js
db.users.find({ name: { $regex: "^[^ABC]" } })
```

---

## 9. OR Operator `(...)|(...)`

### Find names containing either "Bob" or "Eve"

```js
db.users.find({ name: { $regex: "(Bob|Eve)", $options: "i" } })
```

---

## 10. Quantifiers

| Pattern | Meaning         |
| ------- | --------------- |
| `*`     | 0 or more       |
| `+`     | 1 or more       |
| `?`     | 0 or 1          |
| `{n}`   | exactly n       |
| `{n,}`  | at least n      |
| `{n,m}` | between n and m |

### Example: Email with at least 2 digits

```js
db.users.find({ email: { $regex: "\\d{2,}" } })
```

---

## 11. Exclude Matching Patterns ($not + regex)

### Find emails **not** ending with `.com`

```js
db.users.find({ email: { $not: /\.com$/ } })
```

---

## 12. Using Regex in Arrays

Sample Data

```js
db.products.insertMany([
 { name: "Apple iPhone", tags: ["mobile", "apple", "ios"] },
 { name: "Samsung Galaxy", tags: ["mobile", "android"] },
 { name: "MacBook Air", tags: ["laptop", "apple", "macos"] }
])
```

### Find products tagged with "apple"

```js
db.products.find({ tags: { $regex: "apple", $options: "i" } })
```

---

## 13. Performance Considerations

| Trick                                   | Why                                          |
| --------------------------------------- | -------------------------------------------- |
| Use `^` anchor whenever possible        | Speeds up search                             |
| Avoid leading wildcard like `.*pattern` | Slow scan                                    |
| Use indexes                             | Regex uses indexes if regexp starts with `^` |

### Good (uses index)

```js
db.users.find({ name: /^A/i })
```

### Bad (full scan)

```js
db.users.find({ name: /A/i })
```

---

## 14. Escape Special Characters

To search literal `.`, `?`, `+`, `*`

Use `\`

Example: find `example.com`

```js
db.users.find({ email: { $regex: "example\\.com$" } })
```

---

## Summary Table

| Task           | Query                            |
| -------------- | -------------------------------- |
| Starts with    | `{ field: /^pattern/ }`          |
| Ends with      | `{ field: /pattern$/ }`          |
| Ignore case    | `{ field: /pattern/i }`          |
| Contains       | `{ field: /pattern/ }`           |
| Digit required | `{ field: /\\d/ }`               |
| Not match      | `{ field: { $not: /pattern/ } }` |

---
