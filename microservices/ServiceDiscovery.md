
# **Service Discovery **

Problem in Microservices without Service Discovery-
    In a microservices architecture, you have dozens or hundreds of services (e.g., user-service, order-service, payment-service).
Each service can have multiple instances running on different machines/ports, and these instances can be created or destroyed dynamically (auto-scaling, rolling updates, failures).
Hard-coding IPs/ports in clients is impossible → you need a way for:

Services to register themselves when they start.
Clients to discover the current network locations (IP + port) of a service instance.
Automatically deregister unhealthy/down instances.
Optionally do client-side load balancing.

This is exactly what Service Discovery solves.

1. **Eureka Server** – The Service Registry
2. **Customer Service** – A microservice that registers itself
3. **Order Service** – A microservice that discovers and calls Customer Service dynamically

No hardcoded IPs • Automatic registration • Client-side load balancing • Works with multiple instances

### Project Structure
```
service-discovery-demo/
├── eureka-server         (port 8761)
├── customer-service      (port 8081)
└── order-service         (port 8082)
```

### Prerequisites
- Java 17 or 21
- Maven 3.8+
- IDE (IntelliJ/VS Code/Eclipsed)

### Step 1: Eureka Server (Service Registry)

#### 1.1 pom.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.example</groupId>
    <artifactId>eureka-server</artifactId>
    <version>1.0.0</version>
    <packaging>jar</packaging>
    <name>Eureka Server</name>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>3.3.2</version>
        <relativePath/>
    </parent>

    <properties>
        <java.version>17</java.version>
        <spring-cloud.version>2024.0.0</spring-cloud.version>
    </properties>

    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>${spring-cloud.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
</project>
```

#### 1.2 Main Application Class
```java
package com.example.eurekaserver;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.server.EnableEurekaServer;

@SpringBootApplication
@EnableEurekaServer
public class EurekaServerApplication {
    public static void main(String[] args) {
        SpringApplication.run(EurekaServerApplication.class, args);
    }
}
```

#### 1.3 src/main/resources/application.yml
```yaml
server:
  port: 8761

eureka:
  instance:
    hostname: localhost
  client:
    register-with-eureka: false
    fetch-registry: false
    service-url:
      defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/

spring:
  application:
    name: eureka-server
```

Start → Open http://localhost:8761 → You’ll see the beautiful Eureka dashboard.

### Step 2: Customer Service (Registers with Eureka)

#### 2.1 pom.xml
```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>3.3.2</version>
</parent>

<properties>
    <java.version>17</java.version>
    <spring-cloud.version>2024.0.0</spring-cloud.version>
</properties>

<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-dependencies</artifactId>
            <version>${spring-cloud.version}</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>

<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
</dependencies>
```

#### 2.2 Main Application Class
```java
package com.example.customer;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.EnableEurekaClient;

@SpringBootApplication
@EnableEurekaClient
public class CustomerServiceApplication {
    public static void main(String[] args) {
        SpringApplication.run(CustomerServiceApplication.class, args);
    }
}
```

#### 2.3 application.yml
```yaml
server:
  port: 8081

spring:
  application:
    name: customer-service   # This name will appear in Eureka

eureka:
  client:
    service-url:
      defaultZone: http://localhost:8761/eureka/
  instance:
    prefer-ip-address: true
    lease-renewal-interval-in-seconds: 30
```

#### 2.4 REST Controller
```java
package com.example.customer.controller;

import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/api/customers")
public class CustomerController {

    @GetMapping("/{id}")
    public Customer getCustomer(@PathVariable Long id) {
        return new Customer(id, "John Doe " + id, "john" + id + "@example.com");
    }
}

record Customer(Long id, String name, String email) {}
```

Start this service → Go to http://localhost:8761 → You will see **CUSTOMER-SERVICE** with status **UP**.

### Step 3: Order Service (Discovers Customer Service using Feign)

#### 3.1 pom.xml (add OpenFeign)
```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-openfeign</artifactId>
    </dependency>
</dependencies>
```

#### 3.2 Main Application Class
```java
package com.example.order;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.EnableEurekaClient;
import org.springframework.cloud.openfeign.EnableFeignClients;

@SpringBootApplication
@EnableEurekaClient
@EnableFeignClients   // Scans for @FeignClient interfaces
public class OrderServiceApplication {
    public static void main(String[] args) {
        SpringApplication.run(OrderServiceApplication.class, args);
    }
}
```

#### 3.3 application.yml
```yaml
server:
  port: 8082

spring:
  application:
    name: order-service

eureka:
  client:
    service-url:
      defaultZone: http://localhost:8761/eureka/
```

#### 3.4 Feign Client (The Magic of Discovery)
```java
package com.example.order.client;

import org.springframework.cloud.openfeign.FeignClient;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;

@FeignClient(name = "customer-service")  // Exact spring.application.name of Customer Service
public interface CustomerClient {

    @GetMapping("/api/customers/{id}")
    CustomerDto getCustomerById(@PathVariable("id") Long id);
}

record CustomerDto(Long id, String name, String email) {}
```

#### 3.5 Order Controller
```java
package com.example.order.controller;

import com.example.order.client.CustomerClient;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/api/orders")
public class OrderController {

    private final CustomerClient customerClient;

    public OrderController(CustomerClient customerClient) {
        this.customerClient = customerClient;
    }

    @GetMapping("/{orderId}/customer")
    public String getCustomerForOrder(@PathVariable Long orderId) {
        var customer = customerClient.getCustomerById(orderId);
        return String.format("Order %d belongs to %s (%s)", 
                orderId, customer.name(), customer.email());
    }
}
```

### Final Test

Start in this order:
1. Eureka Server (8761)
2. Customer Service (8081)
3. Order Service (8082)

Open browser or use curl:
```bash
curl http://localhost:8082/api/orders/7/customer
```

**Expected Output:**
```
Order 7 belongs to John Doe 7 (john7@example.com)
```

Even if you start 5 instances of customer-service on different ports, order-service will automatically load-balance across all healthy instances — **no code change required**.

### What You Achieved

| Feature                        | Implemented By                          |
|--------------------------------|-----------------------------------------|
| Service Registry               | Eureka Server                           |
| Service Registration           | `@EnableEurekaClient` + `spring.application.name` |
| Dynamic Discovery              | `@FeignClient(name = "customer-service")` |
| Client-side Load Balancing     | Spring Cloud LoadBalancer (default in 2024+) |
| Zero hardcoded URLs            | Full service discovery                  |
