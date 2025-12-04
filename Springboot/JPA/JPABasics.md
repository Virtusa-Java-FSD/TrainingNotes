
# 1. What is JPA / Why use it?

* **JPA** (Java Persistence API) is a specification for mapping Java objects to relational database tables.
* **Hibernate** is the most common JPA implementation.
* Spring Boot + JPA gives you:

  * Object-relational mapping (ORM)
  * Repository abstractions (Spring Data JPA)
  * Easy configuration and conventions
  * Transaction management

---

# 2. Project setup (Maven)

Add dependencies in `pom.xml`:

```xml
<!-- Spring Boot Starter Data JPA -->
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>

<!-- Database driver - In memory (example: H2 for demo or MySQL/Postgres for prod) -->
<!-- H2 for local/demo -->
<dependency>
  <groupId>com.h2database</groupId>
  <artifactId>h2</artifactId>
  <scope>runtime</scope>
</dependency>

<!-- Lombok (optional) -->
<dependency>
  <groupId>org.projectlombok</groupId>
  <artifactId>lombok</artifactId>
  <optional>true</optional>
</dependency>
```

If using MySQL:

```xml
<dependency>
  <groupId>mysql</groupId>
  <artifactId>mysql-connector-java</artifactId>
  <scope>runtime</scope>
</dependency>
```

---

# 3. Basic configuration (application.properties)

For in-memory demo with H2:

```properties
spring.datasource.url=jdbc:h2:mem:demo;DB_CLOSE_DELAY=-1
spring.datasource.username=sa
spring.datasource.password=
spring.datasource.driverClassName=org.h2.Driver

spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.format_sql=true
```

For production use (MySQL example):

```properties
spring.datasource.url=jdbc:mysql://localhost:3306/demo_db?useSSL=false&serverTimezone=UTC
spring.datasource.username=root
spring.datasource.password=secret

spring.jpa.hibernate.ddl-auto=validate
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL8Dialect
```

**Notes**

* `ddl-auto`: `create` / `update` / `validate` / `none`. Use `update` for development, `validate` for production.
* `show-sql=true` prints SQL — great for learning.

---

# 4. Entity basics

A simple `Student` entity example with explanations.

```java
package com.example.demo.model;

import jakarta.persistence.*;
import java.time.LocalDate;

@Entity
@Table(name = "students")
public class Student {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY) // auto-increment
    private Long id;

    @Column(nullable = false, length = 100)
    private String name;

    private String city;

    @Column(name = "enrolled_on")
    private LocalDate enrolledOn;

    // constructors, getters, setters
}
```

**Key annotations**

* `@Entity` — marks a JPA entity.
* `@Table` — optional, specifies DB table name and constraints.
* `@Id` — primary key.
* `@GeneratedValue` — primary key generation strategy.
* `@Column` — column-level configuration.

---

# 5. Spring Data JPA Repository

Create an interface — Spring Data implements it at runtime.

```java
package com.example.demo.repository;

import com.example.demo.model.Student;
import org.springframework.data.jpa.repository.JpaRepository;
import java.util.List;

public interface StudentRepository extends JpaRepository<Student, Long> {
    List<Student> findByCity(String city);
    List<Student> findByNameContainingIgnoreCase(String partialName);
}
```

**Notes**

* `JpaRepository<T, ID>` provides CRUD, paging, sorting.
* Method name conventions create queries automatically (derived queries).

---

# 6. Service layer (recommended)

Keep business logic and transactions out of controllers.

```java
package com.example.demo.service;

import com.example.demo.model.Student;
import com.example.demo.repository.StudentRepository;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;
import java.util.List;
import java.util.Optional;

@Service
public class StudentService {

    private final StudentRepository repo;

    public StudentService(StudentRepository repo) {
        this.repo = repo;
    }

    @Transactional(readOnly = true)
    public List<Student> getAll() {
        return repo.findAll();
    }

    @Transactional(readOnly = true)
    public Optional<Student> getById(Long id) {
        return repo.findById(id);
    }

    @Transactional
    public Student create(Student s) {
        return repo.save(s);
    }

    @Transactional
    public Student update(Long id, Student update) {
        Student existing = repo.findById(id).orElseThrow(() -> new RuntimeException("Not found"));
        existing.setName(update.getName());
        existing.setCity(update.getCity());
        return repo.save(existing);
    }

    @Transactional
    public void delete(Long id) {
        repo.deleteById(id);
    }
}
```

**Transaction notes**

* Use `@Transactional` on service methods.
* `readOnly = true` hints the provider to optimize reads.

---

# 7. Controller (REST API example)

```java
package com.example.demo.controller;

import com.example.demo.model.Student;
import com.example.demo.service.StudentService;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;
import java.net.URI;
import java.util.List;

@RestController
@RequestMapping("/api/students")
public class StudentController {

    private final StudentService svc;

    public StudentController(StudentService svc) { this.svc = svc; }

    @GetMapping
    public List<Student> list() {
        return svc.getAll();
    }

    @GetMapping("/{id}")
    public ResponseEntity<Student> get(@PathVariable Long id) {
        return svc.getById(id).map(ResponseEntity::ok)
                  .orElseGet(() -> ResponseEntity.notFound().build());
    }

    @PostMapping
    public ResponseEntity<Student> create(@RequestBody Student s) {
        Student created = svc.create(s);
        return ResponseEntity.created(URI.create("/api/students/" + created.getId())).body(created);
    }

    @PutMapping("/{id}")
    public ResponseEntity<Student> update(@PathVariable Long id, @RequestBody Student s) {
        Student updated = svc.update(id, s);
        return ResponseEntity.ok(updated);
    }

    @DeleteMapping("/{id}")
    public ResponseEntity<Void> delete(@PathVariable Long id) {
        svc.delete(id);
        return ResponseEntity.noContent().build();
    }
}
```

---

# 8. Query types in Spring Data JPA

## 8.1 Derived query methods (convention-based)

Examples:

* `findByCity(String city)`
* `findByNameAndCity(String name, String city)`
* `findByAgeGreaterThan(int age)`

## 8.2 JPQL (Java Persistence Query Language)

```java
@Query("SELECT s FROM Student s WHERE s.city = :city")
List<Student> findStudentsInCity(@Param("city") String city);
```

## 8.3 Native queries

```java
@Query(value = "SELECT * FROM students WHERE city = :city", nativeQuery = true)
List<Student> findNativeByCity(@Param("city") String city);
```

**Prefer JPQL or derived methods** for portability; use native only when necessary.

---

# 9. Fetch types and performance

* `FetchType.EAGER` — loads related entity immediately (default for `@ManyToOne`).
* `FetchType.LAZY` — loads on demand (recommended for collections).
* Beware N+1 select problem — use `@EntityGraph` or `JOIN FETCH` in queries when you need to fetch associations eagerly.

Example to avoid N+1:

```java
@Query("SELECT s FROM Student s JOIN FETCH s.courses WHERE s.id = :id")
Optional<Student> findByIdWithCourses(@Param("id") Long id);
```

---

# 10. Paging and Sorting

Spring Data makes pagination trivial.

```java
import org.springframework.data.domain.Page;
import org.springframework.data.domain.PageRequest;
import org.springframework.data.domain.Sort;

Page<Student> page = repo.findAll(PageRequest.of(0, 10, Sort.by("name").ascending()));
```

In repository you can return `Page<T>` or `Slice<T>`.

Controller example:

```java
@GetMapping
public Page<Student> list(@RequestParam(defaultValue = "0") int page,
                          @RequestParam(defaultValue = "10") int size) {
    return repo.findAll(PageRequest.of(page, size));
}
```

---

# 11. DTOs and Entity vs API Model

**Don’t expose JPA entities directly** in production APIs. Use DTOs to decouple schema, control fields, and avoid lazy-loading issues.

Example DTO and mapping (simple manual mapping):

```java
public class StudentDto {
    private Long id;
    private String name;
    private String city;
    // getters/setters
}
```

Mapping in service:

```java
public StudentDto toDto(Student s) {
    StudentDto d = new StudentDto();
    d.setId(s.getId());
    d.setName(s.getName());
    d.setCity(s.getCity());
    return d;
}
```

For larger projects use MapStruct for compile-time mapping.

---

#  Exception handling patterns

* Convert JPA exceptions to meaningful API errors.
* Example: handle `EmptyResultDataAccessException` when delete-by-id fails.
* Use `@RestControllerAdvice` to map exceptions to HTTP responses.

---

---

# Common pitfalls and how to fix them

* **LazyInitializationException**: Occurs when accessing lazy association outside transaction. Fix: fetch inside transaction or use DTO projection / `JOIN FETCH`.
* **N+1 problem**: Fix with `@EntityGraph` or `JOIN FETCH`.
* **Unexpected schema changes**: Keep `spring.jpa.hibernate.ddl-auto=validate` in production, and use migration tools (Flyway/Liquibase).
* **Duplicate inserts**: Check cascades and owning side of relationship.

---
