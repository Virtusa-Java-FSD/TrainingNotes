

##  **Section A: String-Based Problems (2 questions)**

### **1. Product Code Normalization — E-Commerce Catalog**

**Theme:** You are managing product data for an online marketplace. Product codes come in inconsistent formats.

**Task:**
Given a list of raw product codes, clean and standardize them:

Rules:

* Remove any non-alphanumeric characters
* Convert to uppercase
* Ensure code length is exactly 8 characters

    * If shorter → pad with `X` at the end
    * If longer → trim to first 8 chars

**Input:**

```
Zara-12x
@nikeRun!!
ad!D344
```

**Output:**

```
ZARA12XX
NIKERUNX
ADD344XX
```

---

### **2. Email Username Comparator — Corporate IT Onboarding**

**Theme:** New employees register email IDs; you must validate their usernames.

**Task:**
Given email IDs, extract usernames and check if any two users have similar names (ignoring case and numbers).

Rule: remove digits & compare strings case-insensitive.

**Input:**

```
ram123@acme.com
Ram@acme.com
ram.prod@acme.com
dinesh_01@acme.com
```

**Output:**

```
Similar usernames detected: ram123, Ram, ram.prod
Unique username: dinesh_01
```

---

## **Section B: Array-Based Problems (2 questions)**

### **3. Stock Price Trend Analyzer — Financial Analytics**

**Theme:** A fintech dashboard must identify increasing stock streaks.

**Task:**
Given an array of daily stock prices, find the **length of the longest strictly increasing streak**.

**Input:**

```
[102, 104, 103, 105, 106, 100, 101, 102, 103]
```

**Output:**

```
Longest increasing streak length: 4
```

---

### **4. Stadium Seat Allocation — Ticketing System**

**Theme:** Stadium booking app needs to assign best available seats.

**Task:**
Given an array of seat numbers and seats already reserved, return the **next closest available seat** greater than the requested one.

If none, return `-1`.

**Input:**

```
Seats: [1,2,3,4,5,6,7,8]
Reserved: [3,4,6]
Request: 4
```

**Output:**

```
Next available seat: 5
```

---

## **Section C: Collections — List & Map (2 questions)**

### **5. Movie Popularity Ranking — Streaming Platform**

**Theme:** Analyze view counts & generate top trending movies.

**Task:**
Given a `List<String>` of movie names (each entry = one watch), create a ranking:

* Count occurrences using `Map<String, Integer>`
* Sort descending by views
* Display top 3 movies

**Input:**

```
[Avatar, Dune, Avatar, Batman, Dune, Avatar, Batman]
```

**Output:**

```
1. Avatar - 3 views
2. Dune - 2 views
3. Batman - 2 views
```

> If tie → lexicographically sort by name.

---

### **6. Customer Feedback Aggregator — Retail CRM**

**Theme:** Collect & classify feedback by sentiment keywords.

**Task:**
Given feedback messages, bucket them into categories using keywords:

* Positive → {good, great, fast, happy}
* Negative → {bad, slow, unhappy, poor}
* Others → Neutral

Use **Map<String, List<String>>** to group.

**Input:**

```
["Delivery was fast", "Product quality poor", "Great packaging", "Unhappy with support"]
```

**Output:**

```
Positive: ["Delivery was fast", "Great packaging"]
Negative: ["Product quality poor", "Unhappy with support"]
Neutral: []
```

---
