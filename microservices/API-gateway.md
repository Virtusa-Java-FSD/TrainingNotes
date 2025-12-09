# Spring Cloud Gateway (API Gateway)

You already have a service discovery setup with:
- Eureka Server (8761)
- Customer Service (8081)
- Order Service (8082)

Now we will add a **modern Spring Cloud Gateway** as the **single entry point** for all external clients.

### Why Do We Need an API Gateway?

| Problem Without Gateway                  | Solved by API Gateway                                      |
|------------------------------------------|------------------------------------------------------------|
| Clients must know all service ports      | Single URL: `http://localhost:8080`                        |
| Different paths per service              | Unified clean paths: `/api/customers`, `/api/orders`       |
| Duplicate cross-cutting logic (auth, rate limiting, CORS) | Centralized in Gateway                                     |
| Load balancing & retries per client      | Done once in Gateway                                       |
| Versioning, routing rules, A/B testing   | Easy with Gateway                                          |

Spring Cloud Gateway (2024–2025) is the **official replacement** for Zuul and is reactive, fast, and fully integrated with Eureka + Spring Cloud LoadBalancer.

### Final Architecture After Adding Gateway

```
External Clients
       ↓
http://localhost:8080  ← Spring Cloud Gateway (API Gateway)
       ↓ (discovers via Eureka)
┌─────────────────┬─────────────────┐
│ customer-service │ order-service   │
│ (multiple instances)            │
└─────────────────┴─────────────────┘
            ↑          ↑
        Eureka Server (8761)
```

### Step-by-Step: Add API Gateway to Your Existing Project

#### 1. Create New Module: `api-gateway`

Project structure now becomes:
```
service-discovery-demo/
├── eureka-server
├── customer-service
├── order-service
└── api-gateway          ← NEW (port 8080)
```

#### 1.1 pom.xml – api-gateway
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.example</groupId>
    <artifactId>api-gateway</artifactId>
    <version>1.0.0</version>

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
        <!-- Gateway (Reactive) -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-gateway</artifactId>
        </dependency>

        <!-- Eureka Client (so Gateway can discover services) -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>

        <!-- LoadBalancer (already included, but explicit is fine) -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-loadbalancer</artifactId>
        </dependency>
    </dependencies>
</project>
```

#### 1.2 Main Application Class
```java
package com.example.apigateway;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.EnableEurekaClient;

@SpringBootApplication
@EnableEurekaClient   // Registers itself + discovers others
public class ApiGatewayApplication {
    public static void main(String[] args) {
        SpringApplication.run(ApiGatewayApplication.class, args);
    }
}
```

#### 1.3 application.yml – The Heart of Routing
```yaml
server:
  port: 8080

spring:
  application:
    name: api-gateway

  cloud:
    gateway:
      discovery:
        locator:
          enabled: true                    # ← Magic: auto route via serviceId
          lower-case-service-id: true      # allows customer-service → /customer-service/**
      
      routes:
        # Optional: Explicit routes (more control)
        - id: customer-service
          uri: lb://customer-service           # lb = load-balanced via Eureka
          predicates:
            - Path=/api/customers/**, /customers/**
          filters:
            - StripPrefix=1                    # removes /api from beginning

        - id: order-service
          uri: lb://order-service
          predicates:
            - Path=/api/orders/**, /orders/**
          filters:
            - StripPrefix=1

eureka:
  client:
    service-url:
      defaultZone: http://localhost:8761/eureka/
  instance:
    prefer-ip-address: true
```

### Explanation of Key Parts

| Feature                            | What It Does                                                                 |
|------------------------------------|-------------------------------------------------------------------------------|
| `lb://customer-service`            | Tells Gateway: “Go find this service in Eureka and load-balance”              |
| `discovery.locator.enabled: true`  | Auto-creates routes like `http://gateway:8080/customer-service/...`           |
| `StripPrefix=1`                    | Removes first part of path so `/api/customers/5` → `/customers/5` to service  |
| `lower-case-service-id: true`      | Allows clean URLs without uppercase                                           |

### Final Clean Public URLs (via Gateway Only)

| Old Direct URL                            | New Clean URL via Gateway                      |
|-------------------------------------------|------------------------------------------------|
| `http://localhost:8081/api/customers/10`  | → `http://localhost:8080/api/customers/10`     |
| `http://localhost:8082/api/orders/7/customer` | → `http://localhost:8080/api/orders/7/customer` |

Clients now talk **only** to port 8080 — everything else is hidden!

### Test the Full Flow

Start all four applications:
1. Eureka Server (8761)
2. Customer Service (8081)
3. Order Service (8082)
4. API Gateway (8080)

#### Test 1: Get Customer via Gateway
```bash
curl http://localhost:8080/api/customers/25
```
→ Returns John Doe 25 from customer-service (load-balanced if multiple instances)

#### Test 2: Get Order → Customer via Gateway
```bash
curl http://localhost:8080/api/orders/25/customer
```
→ Order Service receives request → discovers customer-service via Eureka → returns full info  
All through the single gateway!

### Bonus: Even Simpler Routing (If You Love Auto-Magic)

If you **only** enable discovery locator and remove explicit routes:
```yaml
spring:
  cloud:
    gateway:
      discovery:
        locator:
          enabled: true
          lower-case-service-id: true
```
Then these URLs also work automatically:
```
http://localhost:8080/customer-service/api/customers/1
http://localhost:8080/order-service/api/orders/1/customer
```

Most teams prefer **explicit routes** (as shown above) for clean public APIs.

### Summary: What You Now Have (Full Production Pattern)

```
Clients
   ↓ (only knows http://api.example.com or localhost:8080)
┌───────────────────────┐
│   Spring Cloud Gateway  │ ← Routing, Auth, Rate Limiting, Logging
└───────────────────────┘
           ↓ (Eureka Discovery + Load Balancing)
   ┌───────────────┬───────────────┐
   │               │               │
customer-service   order-service   payment-service (future)
   (many instances) (many instances)
```

You now have the **complete modern Spring Boot microservices foundation** used by thousands of companies:

Eureka → Service Discovery  
Spring Cloud Gateway → API Gateway  
OpenFeign / WebClient → Internal calls  
Spring Cloud LoadBalancer → Client-side LB
