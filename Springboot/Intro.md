# **Spring Boot – Introduction**

## **1. What Is Spring Boot?**

Spring Boot is a framework built on top of the Spring Framework. Its goal is to make Java application development:

* **Fast** – minimal configuration
* **Easy** – auto-configuration handles most defaults
* **Production-ready** – includes metrics, health checks, logging, embedded servers
* **Stand-alone** – no need to deploy WAR files to external servers—Spring Boot embeds Tomcat/Jetty/Netty.

### Why Spring Boot?

Traditional Spring apps require large XML configs, manual dependency management, and deployment complexity.
Spring Boot removes all this by providing:

* **Opinionated defaults**
* **Auto-configuration** (Spring figures out configuration automatically)
* **Starter dependencies** (bundled libraries for common use cases)
* **Embedded server**
* **Production tools** (actuator endpoints)

---

## **2. Core Concepts of Spring Boot**

### **2.1 Auto-Configuration**

Spring Boot scans your classpath and automatically configures beans.
Example:

* If **spring-boot-starter-web** is present → Spring auto-configures:

    * DispatcherServlet
    * Embedded Tomcat
    * Jackson JSON mapper
    * MVC configuration

You avoid a lot of manual setup.

---

### **2.2 Starter Dependencies**

Spring Boot provides "starters"—bundles of dependencies for common tasks.

Examples:

| Starter Name                     | Purpose                    |
| -------------------------------- | -------------------------- |
| `spring-boot-starter-web`        | Build REST APIs / MVC apps |
| `spring-boot-starter-data-jpa`   | JPA/Hibernate integration  |
| `spring-boot-starter-test`       | Testing with JUnit5        |
| `spring-boot-starter-security`   | Security & authentication  |
| `spring-boot-starter-validation` | Bean validation            |

These remove version conflicts and simplify dependency management.

---

### **2.3 Embedded Server**

Spring Boot apps run directly via:

```
java -jar app.jar
```

because the internal server (Tomcat by default) is embedded.
No need to install Tomcat separately.

---

### **2.4 Opinionated Defaults**

Spring Boot chooses sensible defaults. Example:

* JSON → Jackson
* Server port → 8080
* Logging → Logback
* Database pool → HikariCP

You can override everything, but it works out-of-the-box.

---

### **2.5 Spring Boot Actuator (Optional but Powerful)**

Adds production management endpoints:

* `/actuator/health`
* `/actuator/info`
* `/actuator/metrics`
* `/actuator/env`

Useful for DevOps and monitoring.

---

# **3. Spring Boot Project Setup (Beginner Friendly Method)**

There are **3 main ways** to create a Spring Boot project:

1. **Spring Initializr (Web) – recommended**
2. **Using an IDE**
3. **Using the command line**

Below is the detailed explanation of each method.

---

# **4. Create a Spring Boot Project via Spring Initializr (Step-by-Step)**

Open:
[https://start.spring.io](https://start.spring.io)

### **4.1 Project Metadata**

Fill these fields:

| Field               | Example       | Explanation              |
| ------------------- | ------------- | ------------------------ |
| Project             | Maven         | Choose Maven or Gradle   |
| Language            | Java          |                          |
| Spring Boot Version | Latest stable |                          |
| Group               | com.example   | Package namespace        |
| Artifact            | demo-app      | Name of the application  |
| Name                | demo-app      | Project name             |
| Packaging           | Jar           | Jar recommended          |
| Java                | 17 or above   | LTS versions recommended |

---

### **4.2 Add Dependencies**

For a simple REST app:

* **Spring Web**
* **Spring Boot DevTools** (optional, hot reload)
* **Lombok** (optional but useful)

---

### **4.3 Download & Import Project**

Click **Generate**, extract the zip, open in IntelliJ or Eclipse.

---

# **5. Project Structure (Explained in Detail)**

```
demo-app
 └── src
      └── main
           └── java
                └── com.example.demoapp
                       └── DemoAppApplication.java
           └── resources
                ├── application.properties
                ├── static
                └── templates
```

### Important Folders:

### **src/main/java**

Contains your application code.

### **src/main/resources**

Contains:

| Folder                       | Purpose                      |
| ---------------------------- | ---------------------------- |
| application.properties / yml | App configuration            |
| static/                      | Static files (HTML, JS, CSS) |
| templates/                   | Thymeleaf templates          |
| banner.txt                   | Custom startup banner        |

---

### **Main Class**

```java
@SpringBootApplication
public class DemoAppApplication {
    public static void main(String[] args) {
        SpringApplication.run(DemoAppApplication.class, args);
    }
}
```

### What @SpringBootApplication Includes:

It is a combination of three annotations:

1. `@Configuration`
2. `@EnableAutoConfiguration`
3. `@ComponentScan`

---

# **6. Create Your First Controller**

Inside `com.example.demoapp.controller`:

```java
package com.example.demoapp.controller;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class HelloController {

    @GetMapping("/hello")
    public String sayHello() {
        return "Hello from Spring Boot!";
    }
}
```

---

# **7. Running the Application**

### **Method 1: From IDE**

Run the main class:
`DemoAppApplication.java` → **Run**

### **Method 2: Using Maven**

```bash
mvn spring-boot:run
```

### **Method 3: Build & Run Jar**

```
mvn clean package
java -jar target/demo-app-0.0.1-SNAPSHOT.jar
```

---

# **8. Change Default Server Port**

In `application.properties`:

```
server.port=9090
```

---

# **9. Create a Simple REST API – Step by Step**

### DTO Layer

```java
public class Student {
    private int id;
    private String name;
    private String city;

    // getters and setters
}
```

### Controller

```java
@RestController
@RequestMapping("/students")
public class StudentController {

    @GetMapping("/{id}")
    public Student getStudent(@PathVariable int id) {
        Student s = new Student();
        s.setId(id);
        s.setName("John Doe");
        s.setCity("Chennai");
        return s;
    }
}
```

---

# **10. Add Validation (Optional)**

Enable validation:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-validation</artifactId>
</dependency>
```

Example:

```java
public class Student {

    @NotBlank
    private String name;

    @Min(1)
    private int id;
}
```

---

# **11. Key Spring Boot Annotations (High Level)**

| Annotation        | Meaning                |
| ----------------- | ---------------------- |
| `@RestController` | Returns JSON responses |
| `@Controller`     | Returns views (HTML)   |
| `@Service`        | Business layer         |
| `@Repository`     | DAO layer              |
| `@Component`      | Generic bean           |
| `@Autowired`      | Dependency Injection   |
| `@GetMapping`     | HTTP GET               |
| `@PostMapping`    | HTTP POST              |
| `@PutMapping`     | HTTP PUT               |
| `@DeleteMapping`  | HTTP DELETE            |
| `@RequestParam`   | Query param            |
| `@PathVariable`   | URL path variable      |

---

# **12. Troubleshooting Common Issues**

### ❗Port Already in Use

```
server.port=8081
```

or kill the existing process.

### ❗Lombok not working

Enable annotation processing in IDE.

### ❗404 Not found

Usually missing:
`@RestController` or wrong URL mapping.

---

# **13. Summary**

By now you understand:

* What Spring Boot is
* Why it exists
* How auto-configuration & starters work
* How to create & run a project
* How to build your first REST API
* Core annotations & configuration

