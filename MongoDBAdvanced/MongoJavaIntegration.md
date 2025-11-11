

# **MongoDB Integration with Java **

## **Introduction**

MongoDB is a popular NoSQL, document-oriented database designed for scalability, high performance, and flexible schema modeling. Unlike traditional relational databases that store data in rows and tables, MongoDB stores information in **BSON documents** (Binary JSON), organized within **collections**.
This tutorial focuses on integrating MongoDB with Java using the official MongoDB Java driver, designing a clean architecture using the **Singleton** and **DAO** patterns, and implementing CRUD operations along with common query patterns.

---

## **1. Why MongoDB for Java Applications**

### Key Characteristics of MongoDB

* **Schema flexibility**: Fields can differ across documents, enabling agile schema evolution.
* **JSON-like data model**: Naturally maps to Java objects.
* **Horizontal scalability (Sharding)**: Suitable for large-scale distributed systems.
* **High-performance reads/writes** for real-time applications.
* **Rich querying capabilities** including indexing, aggregation, and geospatial queries.

### When to use MongoDB in Java Systems

* Microservices and distributed systems
* Event-driven applications
* Content management, catalogs, logs, user profiles
* Systems requiring fast iteration and schema evolution

---

## **2. MongoDB Java Driver Overview**

MongoDB provides multiple Java drivers; the most commonly used are:

| Component                                          | Description              | Use Case                         |
| -------------------------------------------------- | ------------------------ | -------------------------------- |
| Sync Driver (`mongodb-driver-sync`)                | Traditional blocking API | Standard enterprise apps         |
| Reactive Driver (`mongodb-driver-reactivestreams`) | Reactive Streams support | High-throughput reactive systems |
| Spring Data MongoDB                                | Spring abstraction layer | Spring Boot applications         |

This guide uses the **sync driver** for conceptual clarity.

---

## **3. Development Setup**

### **Maven Dependency**

Add the following dependency to `pom.xml`:

```xml
<dependency>
    <groupId>org.mongodb</groupId>
    <artifactId>mongodb-driver-sync</artifactId>
    <version>4.11.0</version>
</dependency>
```

### **MongoDB Environment**

You may use either:

* A local MongoDB installation, or
* MongoDB Atlas (cloud instance)

Connection strings differ slightly; for local development default connection is:

```
mongodb://localhost:27017
```

---

## **4. Connection Lifecycle in Java — Singleton Pattern**

### Rationale

Managing database connections efficiently is essential. Opening new connections repeatedly introduces latency and resource waste.

The **Singleton pattern** ensures:

* A single instance of the MongoDB client is created
* Connection reuse
* Controlled shutdown and resource management

### Implementation

**MongoDBConnection.java**

```java
package com.example.mongo;

import com.mongodb.client.MongoClient;
import com.mongodb.client.MongoClients;
import com.mongodb.client.MongoDatabase;

public class MongoDBConnection {

    private static MongoClient mongoClient;
    private static final String CONNECTION_STRING = "mongodb://localhost:27017";
    private static final String DB_NAME = "training_db";

    private MongoDBConnection() { }

    public static MongoClient getMongoClient() {
        if (mongoClient == null) {
            mongoClient = MongoClients.create(CONNECTION_STRING);
        }
        return mongoClient;
    }

    public static MongoDatabase getDatabase() {
        return getMongoClient().getDatabase(DB_NAME);
    }
}
```

---

## **5. Domain Model (POJO)**

A POJO is used for logical representation of data.
Note: Without codec/mapper configuration, conversion between `Document` and POJO must be manual.

```java
package com.example.model;

public class Student {
    private String id;
    private String name;
    private int age;
    private String course;

    public Student() { }

    public Student(String id, String name, int age, String course) {
        this.id = id;
        this.name = name;
        this.age = age;
        this.course = course;
    }

    // Getters and setters
}
```

---

## **6. Data Access Layer — DAO Pattern**

The **DAO (Data Access Object)** pattern abstracts database interaction and promotes separation of concerns.
This isolates persistence logic from business logic and improves maintainability and testability.

**StudentDAO.java**

```java
package com.example.dao;

import com.example.model.Student;
import com.example.mongo.MongoDBConnection;
import com.mongodb.client.MongoCollection;
import com.mongodb.client.MongoDatabase;
import org.bson.Document;
import com.mongodb.client.model.Filters;

import java.util.ArrayList;
import java.util.List;

public class StudentDAO {

    private MongoCollection<Document> collection;

    public StudentDAO() {
        MongoDatabase db = MongoDBConnection.getDatabase();
        collection = db.getCollection("students");
    }

    // CREATE
    public void insertStudent(Student student) {
        Document doc = new Document("_id", student.getId())
                .append("name", student.getName())
                .append("age", student.getAge())
                .append("course", student.getCourse());
        collection.insertOne(doc);
    }

    // READ
    public Student getStudentById(String id) {
        Document doc = collection.find(Filters.eq("_id", id)).first();
        if (doc == null) return null;
        return new Student(
                doc.getString("_id"),
                doc.getString("name"),
                doc.getInteger("age"),
                doc.getString("course")
        );
    }

    // UPDATE
    public void updateCourse(String id, String course) {
        collection.updateOne(
                Filters.eq("_id", id),
                new Document("$set", new Document("course", course))
        );
    }

    // DELETE
    public void deleteStudent(String id) {
        collection.deleteOne(Filters.eq("_id", id));
    }

    // READ ALL
    public List<Student> getAllStudents() {
        List<Student> list = new ArrayList<>();
        for (Document doc : collection.find()) {
            list.add(new Student(
                    doc.getString("_id"),
                    doc.getString("name"),
                    doc.getInteger("age"),
                    doc.getString("course")
            ));
        }
        return list;
    }
}
```

---

## **7. Executing CRUD Operations**

**MainApp.java**

```java
public class MainApp {
    public static void main(String[] args) {

        StudentDAO dao = new StudentDAO();

        dao.insertStudent(new Student("S101", "Ram", 22, "Java"));
        dao.insertStudent(new Student("S102", "Priya", 21, "Python"));

        System.out.println(dao.getAllStudents());

        Student s = dao.getStudentById("S101");
        System.out.println(s.getName());

        dao.updateCourse("S101", "Spring Boot");

        dao.deleteStudent("S102");
    }
}
```

---

## **8. Querying in MongoDB via Java**

### Filtering

```java
collection.find(Filters.gt("age", 20));
collection.find(Filters.and(Filters.eq("course", "Java"), Filters.lt("age", 30)));
```

### Projection

```java
collection.find().projection(Projections.include("name", "course"));
```

### Sorting

```java
collection.find().sort(Sorts.ascending("age"));
```

### Pagination

```java
collection.find().skip(10).limit(5);
```

### Aggregation

```java
List<Document> pipeline = List.of(
    new Document("$group", new Document("_id", "$course")
    .append("count", new Document("$sum", 1)))
);
collection.aggregate(pipeline);
```

### Indexing

```java
collection.createIndex(Indexes.ascending("name"));
```

---

## **9. Conceptual Notes and Best Practices**

### Object Mapping

While manual conversion works for learning, production systems benefit from:

* CodecRegistry with POJO codecs
* Spring Data MongoDB repositories
* Morphia ORM for MongoDB

### Index Use Cases

Indexes should be created on fields that are frequently queried or sorted upon to optimize performance.
Avoid excessive indexing as it consumes memory and slows write operations.

### Schema Design Considerations

Despite being schema-flexible, good design remains important:

* Embed documents when data is tightly coupled and accessed together.
* Reference documents when relationships are large or loosely coupled.

### Connection Lifecycle

Always reuse MongoClient; it maintains internal pools.
Closing connections frequently is inefficient and unnecessary.

---

