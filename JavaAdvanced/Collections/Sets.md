

## ✅ **What is a Set in Java?**

`Set<E>` is a Collection that:

* **Does NOT allow duplicates**
* Does NOT guarantee index-based access (no `get(index)`)
* Can contain `null` (except `TreeSet`)
* Represents **unique elements**

Hierarchy:

```
Collection → Set
           ├── HashSet
           ├── LinkedHashSet
           └── TreeSet  (SortedSet)
```

---

## ✅ **When to Use Set?**

| Scenario                        | Why Set?                 |
| ------------------------------- | ------------------------ |
| Unique values                   | No duplicates guaranteed |
| Fast lookup / membership check  | Hash-based               |
| Remove duplicates from list     | Common real case         |
| Store unique users, emails, IDs | Real app scenarios       |

---

## ✅ **Types of Sets**

| Set Type          | Order                     | Null Allowed | Performance             |
| ----------------- | ------------------------- | ------------ | ----------------------- |
| **HashSet**       | No order                  | Yes          | Fastest                 |
| **LinkedHashSet** | Maintains insertion order | Yes          | Slightly slower         |
| **TreeSet**       | Sorted ascending          | No NULL      | Slowest (balanced tree) |

---

## ✅ **Common Set Methods**

| Action  | Method                               |
| ------- | ------------------------------------ |
| Add     | `add()`, `addAll()`                  |
| Remove  | `remove()`, `removeAll()`, `clear()` |
| Check   | `contains()`, `isEmpty()`            |
| Size    | `size()`                             |
| Iterate | `iterator()`, `for-each`             |

---

## ✅ **HashSet Full Demo**

### 👉 No order, fastest

```java
import java.util.*;

public class HashSetDemo {
    public static void main(String[] args) {
        Set<String> techs = new HashSet<>();

        techs.add("Java");
        techs.add("Spring");
        techs.add("Microservices");
        techs.add("Java"); // duplicate ignored

        System.out.println(techs);

        System.out.println("Contains Spring? " + techs.contains("Spring"));

        techs.remove("Microservices");
        System.out.println("After removal: " + techs);

        System.out.println("Size: " + techs.size());

        for (String t : techs) {
            System.out.println("Iterating: " + t);
        }
    }
}
```

---

## ✅ **LinkedHashSet Demo**

### 👉 Maintains insertion order

```java
import java.util.*;

public class LinkedHashSetDemo {
    public static void main(String[] args) {
        Set<String> ordered = new LinkedHashSet<>();

        ordered.add("API");
        ordered.add("DB");
        ordered.add("Cache");
        ordered.add("API");

        System.out.println(ordered); // [API, DB, Cache]
    }
}
```

---

## ✅ **TreeSet Demo**

### 👉 Sorted set, no `null`

```java
import java.util.*;

public class TreeSetDemo {
    public static void main(String[] args) {
        Set<Integer> numbers = new TreeSet<>();

        numbers.add(4);
        numbers.add(1);
        numbers.add(3);
        numbers.add(2);

        System.out.println(numbers); // [1, 2, 3, 4]

        System.out.println("First: " + ((TreeSet<Integer>)numbers).first());
        System.out.println("Last: " + ((TreeSet<Integer>)numbers).last());
    }
}
```

---

## ✅ **Set Operations (Important!)**

### ⭐ **Union**

```java
Set<Integer> a = new HashSet<>(Arrays.asList(1,2,3));
Set<Integer> b = new HashSet<>(Arrays.asList(3,4,5));

a.addAll(b);
System.out.println(a); // [1,2,3,4,5]
```

### ⭐ **Intersection**

```java
Set<Integer> a = new HashSet<>(Arrays.asList(1,2,3));
Set<Integer> b = new HashSet<>(Arrays.asList(3,4,5));

a.retainAll(b);
System.out.println(a); // [3]
```

### ⭐ **Difference**

```java
Set<Integer> a = new HashSet<>(Arrays.asList(1,2,3));
Set<Integer> b = new HashSet<>(Arrays.asList(3,4,5));

a.removeAll(b);
System.out.println(a); // [1,2]
```

---

## ✅ **Remove Duplicates from a List**

⭐ **Real-world usage**

```java
List<String> names = Arrays.asList("Tom", "Jerry", "Tom", "Mickey");
Set<String> unique = new HashSet<>(names);
System.out.println(unique); // [Tom, Jerry, Mickey]
```

---

## ✅ **Set vs List**

| Feature      | Set                    | List               |
| ------------ | ---------------------- | ------------------ |
| Duplicates   | ❌ No                   | ✅ Yes              |
| Order        | ❌ HS, ✅ LHS, Sorted TS | ✅ Maintains order  |
| Index Access | ❌                      | ✅                  |
| Use Case     | Unique items           | Ordered collection |

---

## ✅ **Performance Comparison**

| Operation | HashSet | LinkedHashSet | TreeSet  |
| --------- | ------- | ------------- | -------- |
| Insert    | O(1)    | O(1)          | O(log n) |
| Search    | O(1)    | O(1)          | O(log n) |
| Order     | No      | Insertion     | Sorted   |

---

## 🧠 **Interview Questions**

| Question               | Quick Answer            |
| ---------------------- | ----------------------- |
| Why Set?               | Unique values           |
| HashSet vs TreeSet     | Speed vs Sorted         |
| LinkedHashSet use case | Unique + maintain order |
| Why no get(index)?     | No indexing in Set      |
| TreeSet null allowed?  | ❌ No                    |

---

## 🎓 **Exercise for Students**

### ❗ Problem:

Given a string:

```
"banana"
```

Print **unique characters in sorted order**.

**Expected output:**

```
a b n
```

Hints:

* Convert to char array
* Insert into `TreeSet`

---

## 💡 Trainer Pro Tips

* Demo with student names, emails, login IDs
* Show duplicates filtering live
* Ask them to implement validation:
  **No duplicate email registration**

---