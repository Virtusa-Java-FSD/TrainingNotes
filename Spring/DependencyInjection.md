
#  **1. What Is Dependency Injection? **

Dependency Injection (DI) is a design pattern where **objects do not create their own dependencies**. Instead, another entity (Spring Container) **injects** those dependencies.

Example:

Instead of doing:

```java
Engine engine = new Engine();
Car car = new Car(engine);
```

You let Spring supply the `Engine` to `Car`.
This makes code:

* more testable
* clean and decoupled
* easy to replace implementations

---

# ----------------------------------------

#  **2. Dependency Injection Types**

# ----------------------------------------

### **A. Constructor Injection**

* Injects dependency via the constructor.
* Recommended for **mandatory** dependencies.
* Makes class immutable.

### **B. Setter Injection**

* Injects dependency via setter methods.
* Recommended for **optional** dependencies.

### **C. Field Injection**

* Injects dependencies directly into fields.
* **Not recommended** except for quick tests or in legacy code.

---

# ----------------------------------------

# **3. Common Example Classes**

# ----------------------------------------

To avoid repetition, we’ll use:

```java
public interface Engine {
    void start();
}
```

```java
public class V8Engine implements Engine {
    @Override
    public void start() {
        System.out.println("V8 engine roaring...");
    }
}
```

And a `Car`:

```java
public class Car {
    private Engine engine;

    public void drive() {
        engine.start();
        System.out.println("Car is driving...");
    }
}
```

---

# ==============================================================

#  **4. Constructor Injection**

# ==============================================================

# ----------------------------------------

# 4.1 Constructor Injection (XML Configuration)

# ----------------------------------------

### **Car with constructor injection**

```java
public class Car {
    private Engine engine;

    public Car(Engine engine) {  // DI happens here
        this.engine = engine;
    }

    public void drive() {
        engine.start();
        System.out.println("Car is driving...");
    }
}
```

### **XML Config**

```xml
<beans>
    <bean id="engine" class="com.example.V8Engine" />

    <bean id="car" class="com.example.Car">
        <constructor-arg ref="engine"/>
    </bean>
</beans>
```

### **Usage in main**

```java
ApplicationContext ctx = new ClassPathXmlApplicationContext("beans.xml");
Car car = ctx.getBean(Car.class);
car.drive();
```

---

# ----------------------------------------

# 4.2 Constructor Injection (Annotation-Based)

# ----------------------------------------

### **Enable component scanning**

```java
@Configuration
@ComponentScan("com.example")
public class AppConfig {}
```

### **Classes**

```java
@Component
public class V8Engine implements Engine {
    public void start() { System.out.println("V8 engine roaring..."); }
}
```

```java
@Component
public class Car {
    private final Engine engine;

    @Autowired   // Constructor Injection
    public Car(Engine engine) {
        this.engine = engine;
    }

    public void drive() {
        engine.start();
        System.out.println("Car is driving...");
    }
}
```

Constructor injection is preferred and does not require `@Autowired` on Spring 4.3+ (optional).

---

# ----------------------------------------

# 4.3 Constructor Injection (Java Config)

# ----------------------------------------

```java
@Configuration
public class AppConfig {

    @Bean
    public Engine engine() {
        return new V8Engine();
    }

    @Bean
    public Car car() {
        return new Car(engine());
    }
}
```

Usage:

```java
AnnotationConfigApplicationContext ctx = new AnnotationConfigApplicationContext(AppConfig.class);
Car car = ctx.getBean(Car.class);
car.drive();
```

---

# ==============================================================

#  **5. Setter Injection**

# ==============================================================

# ----------------------------------------

# 5.1 Setter Injection (XML Configuration)

# ----------------------------------------

### **Updated Car class**

```java
public class Car {
    private Engine engine;

    public void setEngine(Engine engine) {   // Setter DI
        this.engine = engine;
    }

    public void drive() {
        engine.start();
        System.out.println("Car is driving...");
    }
}
```

### **XML Config**

```xml
<beans>
    <bean id="engine" class="com.example.V8Engine" />

    <bean id="car" class="com.example.Car">
        <property name="engine" ref="engine"/>
    </bean>
</beans>
```

---

# ----------------------------------------

# 5.2 Setter Injection (Annotation-Based)

# ----------------------------------------

```java
@Component
public class Car {

    private Engine engine;

    @Autowired
    public void setEngine(Engine engine) {
        this.engine = engine;
    }

    public void drive() {
        engine.start();
        System.out.println("Car is driving...");
    }
}
```

---

# ----------------------------------------

# 5.3 Setter Injection (Java Config)

# ----------------------------------------

```java
@Configuration
public class AppConfig {

    @Bean
    public Engine engine() {
        return new V8Engine();
    }

    @Bean
    public Car car() {
        Car car = new Car();
        car.setEngine(engine());  // Setter injection
        return car;
    }
}
```

---

# ==============================================================

#  **6. Field Injection**

# ==============================================================

 **Not recommended** in production
— bad for testing, immutability, and clarity.

Used in old code or quick demos.

---

# ----------------------------------------

# 6.1 Field Injection (Annotation-Based Only)

# ----------------------------------------

```java
@Component
public class Car {

    @Autowired   // Field DI
    private Engine engine;

    public void drive() {
        engine.start();
        System.out.println("Car is driving...");
    }
}
```

**No XML or Java config support** (it requires annotations).

---

# ==============================================================

#  **7. Comparing All Three DI Types**

# ==============================================================

| Feature                    | Constructor DI | Setter DI   | Field DI |
| -------------------------- | ------------- | ----------- | ------- |
| Recommended                |  Best         |  Optional |  Avoid  |
| Immutability               | Yes           | No          | No      |
| Supports `final` fields    | Yes           | No          | No      |
| Good for unit testing      | Excellent     | Good        | Poor    |
| Framework-independent      | Yes           | Yes         | No      |
| Suitable for optional deps | No            | Yes         | No      |

---

# ==============================================================

#  **8. Real-World Example with Multiple Dependencies**

# ==============================================================

Service:

```java
public class SpaceShipService {
    private Engine engine;
    private NavigationSystem navigation;

    public SpaceShipService(Engine engine, NavigationSystem navigation) {
        this.engine = engine;
        this.navigation = navigation;
    }

    public void launch() {
        navigation.calculateRoute();
        engine.start();
        System.out.println("Spaceship launching...");
    }
}
```

XML:

```xml
<bean id="engine" class="com.example.V8Engine"/>
<bean id="navigation" class="com.example.NavigationSystem"/>

<bean id="spaceShip" class="com.example.SpaceShipService">
    <constructor-arg ref="engine"/>
    <constructor-arg ref="navigation"/>
</bean>
```

Java Config:

```java
@Bean
public SpaceShipService spaceShip() {
    return new SpaceShipService(engine(), navigation());
}
```

Annotation:

```java
@Component
public class SpaceShipService {

    private final Engine engine;
    private final NavigationSystem navigation;

    @Autowired
    public SpaceShipService(Engine engine, NavigationSystem navigation) {
        this.engine = engine;
        this.navigation = navigation;
    }
}
```

---

# ==============================================================

#  **9. Which DI Style Should You Use?**

# ==============================================================

### ✔ **Use Constructor Injection** (Best Practice)

* For all required dependencies
* Makes object immutable
* Easy to test
* Works without frameworks

### ✔ **Use Setter Injection**

* When dependency is optional
* When you want configuration flexibility

###  Avoid Field Injection

* Except in very small apps or legacy code

---
