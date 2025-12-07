# 1. Introduction to Spring Security

Spring Security is a powerful and customizable framework for securing Spring applications. It helps developers handle:

1. Authentication
2. Authorization
3. Password encryption
4. Session management
5. CSRF protection
6. Security filters
7. Role-based access control

It integrates seamlessly with Spring Boot and provides secure defaults.

---

# 2. Authentication vs Authorization

## 2.1 Authentication

Authentication verifies the identity of a user.
Examples:

* Verifying a username and password
* Validating a token
* Checking credentials stored in a database
  Output of authentication: **Principal (the authenticated user)**

## 2.2 Authorization

Authorization determines what an authenticated user is allowed to do.
Examples:

* Accessing admin-only endpoints
* Restricting actions based on roles
* Allowing a user to view only their own data

Flow:

1. Authenticate the user
2. Grant or deny access based on permissions

---

# 3. How Spring Security Works Internally

Spring Security uses a **filter chain** that intercepts every request.

Key filters:

* Authentication filter (extract login details)
* Authorization filter (verify access permissions)
* Exception filters
* CSRF filters

Spring Security uses:

* AuthenticationManager
* UserDetailsService
* PasswordEncoder
* SecurityContext

---

# 4. Setting Up Spring Security in a Spring Boot Application

## 4.1 Add Required Dependencies

### Maven

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-security</artifactId>
    </dependency>

    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>

    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>

    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-j</artifactId>
    </dependency>
</dependencies>
```

---

# 5. Configure MySQL Database

### application.properties

```properties
spring.datasource.url=jdbc:mysql://localhost:3306/security_db
spring.datasource.username=root
spring.datasource.password=yourpassword

spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
```

---

# 6. Creating User Entity and Role Entity

Spring Security requires a user model with username, password, and roles.

## 6.1 Role Entity

```java
import jakarta.persistence.*;

@Entity
public class Role {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;

    // getters and setters
}
```

## 6.2 User Entity

```java
import jakarta.persistence.*;
import java.util.Set;

@Entity
public class User {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String username;
    private String password;

    @ManyToMany(fetch = FetchType.EAGER)
    @JoinTable(
            name = "user_roles",
            joinColumns = @JoinColumn(name = "user_id"),
            inverseJoinColumns = @JoinColumn(name = "role_id")
    )
    private Set<Role> roles;

    // getters and setters
}
```

---

# 7. Create UserRepository

```java
import org.springframework.data.jpa.repository.JpaRepository;

public interface UserRepository extends JpaRepository<User, Long> {
    User findByUsername(String username);
}
```

---

# 8. Implementing UserDetails and UserDetailsService

Spring Security retrieves users via `UserDetailsService`.

## 8.1 Create CustomUserDetails

```java
import org.springframework.security.core.GrantedAuthority;
import org.springframework.security.core.authority.SimpleGrantedAuthority;
import org.springframework.security.core.userdetails.UserDetails;

import java.util.Collection;
import java.util.stream.Collectors;

public class CustomUserDetails implements UserDetails {

    private final User user;

    public CustomUserDetails(User user) {
        this.user = user;
    }

    @Override
    public Collection<? extends GrantedAuthority> getAuthorities() {
        return user.getRoles().stream()
                .map(role -> new SimpleGrantedAuthority(role.getName()))
                .collect(Collectors.toSet());
    }

    @Override
    public String getPassword() {
        return user.getPassword();
    }

    @Override
    public String getUsername() {
        return user.getUsername();
    }

    @Override
    public boolean isAccountNonExpired() {
        return true;
    }

    @Override
    public boolean isAccountNonLocked() {
        return true;
    }

    @Override
    public boolean isCredentialsNonExpired() {
        return true;
    }

    @Override
    public boolean isEnabled() {
        return true;
    }
}
```

## 8.2 Create CustomUserDetailsService

```java
import org.springframework.security.core.userdetails.UserDetails;
import org.springframework.security.core.userdetails.UserDetailsService;
import org.springframework.security.core.userdetails.UsernameNotFoundException;
import org.springframework.stereotype.Service;

@Service
public class CustomUserDetailsService implements UserDetailsService {

    private final UserRepository repo;

    public CustomUserDetailsService(UserRepository repo) {
        this.repo = repo;
    }

    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
        User user = repo.findByUsername(username);
        if (user == null) {
            throw new UsernameNotFoundException("User not found");
        }
        return new CustomUserDetails(user);
    }
}
```

---

# 9. Configure Password Encoding

Password must be stored in encrypted format.

```java
@Bean
public PasswordEncoder passwordEncoder() {
    return new org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder();
}
```

---

# 10. Defining Spring Security Configuration

In Spring Boot 3, we use `SecurityFilterChain`.

```java
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.authentication.AuthenticationManager;
import org.springframework.security.config.annotation.authentication.configuration.AuthenticationConfiguration;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.crypto.password.PasswordEncoder;
import org.springframework.security.web.SecurityFilterChain;

@Configuration
public class SecurityConfig {

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
                .csrf(csrf -> csrf.disable())
                .authorizeHttpRequests(auth -> auth
                        .requestMatchers("/public/**").permitAll()
                        .requestMatchers("/admin/**").hasRole("ADMIN")
                        .requestMatchers("/user/**").hasRole("USER")
                        .anyRequest().authenticated()
                )
                .formLogin();

        return http.build();
    }

    @Bean
    public AuthenticationManager authenticationManager(AuthenticationConfiguration config) throws Exception {
        return config.getAuthenticationManager();
    }
}
```

### Explanation

1. **csrf.disable()**
   Disabled for simplicity. For production, configure CSRF properly.

2. **requestMatchers("/public/**").permitAll()**
   Open endpoints.

3. **hasRole("ADMIN")**
   Role-based authorization.

4. **formLogin()**
   Uses Spring Security's default login page.

---

# 11. Creating a Registration Endpoint

### Registration Controller

```java
import org.springframework.security.crypto.password.PasswordEncoder;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/public")
public class RegistrationController {

    private final UserRepository repo;
    private final PasswordEncoder encoder;

    public RegistrationController(UserRepository repo, PasswordEncoder encoder) {
        this.repo = repo;
        this.encoder = encoder;
    }

    @PostMapping("/register")
    public String register(@RequestBody User user) {
        user.setPassword(encoder.encode(user.getPassword()));
        repo.save(user);
        return "User registered";
    }
}
```

---

# 12. Creating Test Endpoints

### User Endpoint

```java
@RestController
@RequestMapping("/user")
public class UserController {

    @GetMapping("/data")
    public String userData() {
        return "User data accessed";
    }
}
```

### Admin Endpoint

```java
@RestController
@RequestMapping("/admin")
public class AdminController {

    @GetMapping("/data")
    public String adminData() {
        return "Admin data accessed";
    }
}
```

---

# 13. Testing the Flow

1. Register a user using:
   `POST /public/register`
2. Spring encodes the password
3. Login at `/login` using username and password
4. Access protected endpoints

    * `/user/data` for users
    * `/admin/data` only for admins
5. Unauthorized access returns 403

---

# 14. Summary of What You Learned

You now understand:

1. Authentication vs Authorization
2. Spring Security filter chain
3. UserDetailsService and UserDetails
4. BCrypt password encoding
5. Role-based access control
6. MySQL integration with Spring Security
7. Custom login with Spring Security default UI
8. Protecting endpoints using hasRole and permitAll
9. Registering and authenticating users

---
