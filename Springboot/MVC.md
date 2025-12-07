
# **SPRING MVC **

### **Version: Trainer Edition | Fully Explained | Beginner → Advanced**

---

# **1. What is Spring MVC?**

Spring MVC (Model–View–Controller) is a web framework built on top of Spring.
It helps you build **server-side Java web applications** using:

* **Model** → Data, business objects
* **View** → UI layer (Thymeleaf templates)
* **Controller** → Handles requests, processes input, sends model to view

### **Core Ideas**

* Uses **DispatcherServlet** as the front controller
* Maps URLs to controller methods
* Binds form data to Java objects
* Uses **Thymeleaf** as the view engine
* Provides validation, error handling, view rendering, etc.

---

# **2. Spring MVC Request Flow (Very Important)**

```
Browser → DispatcherServlet → HandlerMapping → Controller
         → Service Layer (optional)
         → Model data → ViewResolver → Thymeleaf Template → Browser
```

---

# **3. Setting Up Spring MVC + Thymeleaf**

### **pom.xml**

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>

    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-thymeleaf</artifactId>
    </dependency>

    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-validation</artifactId>
    </dependency>
</dependencies>
```

### **Directory Structure**

```
src/main/
    java/
        controller/
        service/
        model/
        repository/
    resources/
        templates/         ← Thymeleaf HTML pages
        static/            ← JS, CSS, images
        application.yml
```

---

# **4. Creating a Model (Data Layer)**

### **Example: Student Model**

```java
public class Student {

    @NotBlank(message = "Name cannot be empty")
    private String name;

    @Min(value = 18, message = "Age must be at least 18")
    private int age;

    private String course;

    // getters + setters
}
```

---

# **5. Creating a Controller (Very Important)**

### **HomeController**

```java
@Controller
public class HomeController {

    @GetMapping("/")
    public String homePage(Model model) {
        model.addAttribute("message", "Welcome to Spring MVC!");
        return "index";   // maps to templates/index.html
    }
}
```

### What Happens Here?

* User hits `http://localhost:8080/`
* Controller returns view name **index**
* Thymeleaf loads **index.html**
* Model data is displayed

---

# **6. Thymeleaf Basics**

### **index.html**

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <title>Spring MVC</title>
</head>
<body>
<h2 th:text="${message}"></h2>
</body>
</html>
```

### Thymeleaf Key Expressions

| Purpose       | Syntax               |
| ------------- | -------------------- |
| Print value   | `${value}`           |
| Loop          | `th:each`            |
| Form binding  | `th:object`          |
| Input binding | `th:field`           |
| Conditional   | `th:if`, `th:unless` |

---

# **7. Passing Data from Controller → View**

### Controller

```java
@GetMapping("/courses")
public String courses(Model model) {
    List<String> courseList = List.of("Java", "Python", "Spring", "DevOps");
    model.addAttribute("courses", courseList);
    return "courses";
}
```

### Thymeleaf View (courses.html)

```html
<ul>
    <li th:each="c : ${courses}" th:text="${c}"></li>
</ul>
```

---

# **8. Handling Forms with Spring MVC + Thymeleaf**

### Step 1: Show Form

```java
@GetMapping("/student/register")
public String showForm(Model model) {
    model.addAttribute("student", new Student());
    return "student-form";
}
```

### Step 2: Process Form Submission

```java
@PostMapping("/student/register")
public String submitForm(@Valid @ModelAttribute("student") Student student,
                         BindingResult result,
                         Model model) {

    if (result.hasErrors()) {
        return "student-form";
    }

    model.addAttribute("studentData", student);
    return "success";
}
```

---

### Step 3: Thymeleaf Form View

`student-form.html`

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<body>

<form th:action="@{/student/register}"
      th:object="${student}"
      method="post">

    Name: <input th:field="*{name}"> <br>
    <span th:if="${#fields.hasErrors('name')}"
          th:errors="*{name}"></span><br>

    Age: <input th:field="*{age}"> <br>
    <span th:if="${#fields.hasErrors('age')}"
          th:errors="*{age}"></span><br>

    Course: <input th:field="*{course}"><br>

    <button type="submit">Register</button>
</form>

</body>
</html>
```

---

# **9. Showing Submitted Data**

`success.html`

```html
<h3>Registration Successful</h3>

<p>Name: <span th:text="${studentData.name}"></span></p>
<p>Age: <span th:text="${studentData.age}"></span></p>
<p>Course: <span th:text="${studentData.course}"></span></p>
```

---

# **10. Spring MVC Model Binding Explained**

When you use:

```java
@ModelAttribute("student")
```

Spring:

1. Creates a new Student object (or uses existing one)
2. Binds HTML form fields → Student fields
3. Validates using annotations
4. Adds the bound object to the Model

---

# **11. Redirect vs Forward**

### **Redirect (Client → new URL)**

```java
return "redirect:/success";
```

### **Forward (internal dispatch)**

```java
return "forward:/success";
```

---

# **12. Common Thymeleaf Features**

---

### **12.1 th:each Example**

```html
<tr th:each="s : ${students}">
    <td th:text="${s.name}"></td>
    <td th:text="${s.age}"></td>
</tr>
```

---

### **12.2 Conditional Rendering**

```html
<p th:if="${student.age > 18}">Eligible</p>
<p th:unless="${student.age > 18}">Not Eligible</p>
```

---

### **12.3 URL Linking**

```html
<a th:href="@{/student/register}">Register Student</a>
```

---

# **13. Adding Bootstrap to Thymeleaf**

`<link rel="stylesheet" href="/css/bootstrap.min.css">`

Example:

```html
<button class="btn btn-primary">Submit</button>
```

---

# **14. Serving Static Files (Images, CSS, JS)**

Place inside:

```
src/main/resources/static/
```

Access via:

```
/css/style.css
/js/main.js
/images/logo.png
```

---

# **15. Error Handling Page**

`error.html`

```html
<h2>Something went wrong!</h2>
<p th:text="${message}"></p>
```

---

# **16. Controller Advice for Global Errors**

```java
@ControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(Exception.class)
    public String handleError(Exception ex, Model model) {
        model.addAttribute("message", ex.getMessage());
        return "error";
    }
}
```

---

# **17. CRUD Mini Project (Spring MVC + Thymeleaf)**

### **Entity: Course**

### **Operations:**

* Add Course
* List Courses
* Edit Course
* Delete Course

I can provide full project files if you want.

---

# **18. Summary**

| Feature           | Spring MVC Role                          |
| ----------------- | ---------------------------------------- |
| DispatcherServlet | Entry point for all requests             |
| Controller        | Handles business logic and route mapping |
| Model             | Stores data                              |
| ViewResolver      | Determines which HTML to load            |
| Thymeleaf         | Renders dynamic UI                       |
| Validation        | Ensures form correctness                 |
| Redirect/Forward  | Navigation control                       |

---
