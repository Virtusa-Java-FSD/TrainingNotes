# 1. Overview

Modern applications often use **stateless authentication**, especially for REST APIs.
JWT (JSON Web Token) is a compact, URL-safe token used widely for secure stateless authentication between client and server.

Spring Security integrates well with JWT by providing customizable filters and authentication managers.

This learning material covers:

1. JWT Authentication
2. Custom Authentication Filter
3. Custom Authorization Filter
4. Method-level authorization
5. CORS Configuration
6. Secure endpoint design

---

# 2. What is JWT

JWT is a signed token containing user identity and claims. A typical JWT has:

1. Header
2. Payload (username, roles, expiration)
3. Signature

A valid token proves the user identity.

JWT characteristics:

* Stateless
* No server session storage
* Sent in Authorization header
* Short-lived tokens recommended

Example JWT structure:

`xxxxx.yyyyy.zzzzz`

---

# 3. Add Required Dependencies

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

    <dependency>
        <groupId>io.jsonwebtoken</groupId>
        <artifactId>jjwt-api</artifactId>
        <version>0.11.5</version>
    </dependency>
    <dependency>
        <groupId>io.jsonwebtoken</groupId>
        <artifactId>jjwt-impl</artifactId>
        <version>0.11.5</version>
        <scope>runtime</scope>
    </dependency>
    <dependency>
        <groupId>io.jsonwebtoken</groupId>
        <artifactId>jjwt-jackson</artifactId>
        <version>0.11.5</version>
        <scope>runtime</scope>
    </dependency>
</dependencies>
```

---

# 4. User Entity and Role Model

Use your existing models or follow this structure.

```java
@Entity
public class User {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String username;
    private String password;

    @ElementCollection(fetch = FetchType.EAGER)
    private List<String> roles;
}
```

Repository:

```java
public interface UserRepository extends JpaRepository<User, Long> {
    User findByUsername(String username);
}
```

---

# 5. JWT Utility Class

```java
import io.jsonwebtoken.*;
import io.jsonwebtoken.security.Keys;
import org.springframework.stereotype.Component;
import java.security.Key;
import java.util.Date;
import java.util.List;

@Component
public class JwtUtil {

    private final Key key = Keys.secretKeyFor(SignatureAlgorithm.HS256);
    private final long expirationMs = 3600000;

    public String generateToken(String username, List<String> roles) {
        return Jwts.builder()
                .setSubject(username)
                .claim("roles", roles)
                .setExpiration(new Date(System.currentTimeMillis() + expirationMs))
                .signWith(key)
                .compact();
    }

    public Claims extractClaims(String token) {
        return Jwts.parserBuilder()
                .setSigningKey(key)
                .build()
                .parseClaimsJws(token)
                .getBody();
    }
}
```

Explanation:

* Secret key is generated internally. In production, store it securely.
* Token expiry is one hour.
* Claims include username and roles.

---

# 6. Custom Authentication Filter (Login)

This filter validates username/password and generates JWT.

```java
import com.fasterxml.jackson.databind.ObjectMapper;
import jakarta.servlet.*;
import jakarta.servlet.http.*;
import org.springframework.security.authentication.*;
import org.springframework.security.core.Authentication;
import org.springframework.security.web.authentication.UsernamePasswordAuthenticationFilter;

public class JwtAuthenticationFilter extends UsernamePasswordAuthenticationFilter {

    private final AuthenticationManager manager;
    private final JwtUtil jwtUtil;

    public JwtAuthenticationFilter(AuthenticationManager manager, JwtUtil jwtUtil) {
        this.manager = manager;
        this.jwtUtil = jwtUtil;
        setFilterProcessesUrl("/auth/login");
    }

    @Override
    public Authentication attemptAuthentication(HttpServletRequest request, HttpServletResponse response) {
        try {
            LoginRequest login = new ObjectMapper().readValue(request.getInputStream(), LoginRequest.class);
            UsernamePasswordAuthenticationToken token =
                    new UsernamePasswordAuthenticationToken(login.getUsername(), login.getPassword());
            return manager.authenticate(token);
        } catch (Exception ex) {
            throw new RuntimeException("Invalid login request");
        }
    }

    @Override
    protected void successfulAuthentication(
            HttpServletRequest req, HttpServletResponse res, FilterChain chain, Authentication auth) {

        CustomUserDetails user = (CustomUserDetails) auth.getPrincipal();

        String jwt = jwtUtil.generateToken(
                user.getUsername(),
                user.getAuthorities().stream()
                        .map(a -> a.getAuthority())
                        .toList()
        );

        res.setContentType("application/json");
        try {
            res.getWriter().write("{\"token\": \"" + jwt + "\"}");
        } catch (Exception ignored) {}
    }
}
```

LoginRequest DTO:

```java
public class LoginRequest {
    private String username;
    private String password;
}
```

---

# 7. Custom Authorization Filter (JWT Validation)

This filter checks token for every request except login.

```java
import jakarta.servlet.*;
import jakarta.servlet.http.*;
import org.springframework.security.authentication.UsernamePasswordAuthenticationToken;
import org.springframework.security.core.context.SecurityContextHolder;
import org.springframework.web.filter.OncePerRequestFilter;

public class JwtAuthorizationFilter extends OncePerRequestFilter {

    private final JwtUtil jwtUtil;
    private final CustomUserDetailsService service;

    public JwtAuthorizationFilter(JwtUtil jwtUtil, CustomUserDetailsService service) {
        this.jwtUtil = jwtUtil;
        this.service = service;
    }

    @Override
    protected void doFilterInternal(
            HttpServletRequest request, HttpServletResponse response, FilterChain filterChain) {

        String header = request.getHeader("Authorization");

        if (header != null && header.startsWith("Bearer ")) {
            String jwt = header.substring(7);

            try {
                var claims = jwtUtil.extractClaims(jwt);
                String username = claims.getSubject();

                var userDetails = service.loadUserByUsername(username);

                var auth = new UsernamePasswordAuthenticationToken(
                        userDetails,
                        null,
                        userDetails.getAuthorities()
                );

                SecurityContextHolder.getContext().setAuthentication(auth);

            } catch (Exception ignored) {}
        }

        try {
            filterChain.doFilter(request, response);
        } catch (Exception ignored) {}
    }
}
```

---

# 8. Security Configuration

```java
@Configuration
@EnableMethodSecurity
public class SecurityConfig {

    private final JwtUtil jwtUtil;
    private final CustomUserDetailsService userService;

    public SecurityConfig(JwtUtil jwtUtil, CustomUserDetailsService userService) {
        this.jwtUtil = jwtUtil;
        this.userService = userService;
    }

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        JwtAuthenticationFilter authFilter =
                new JwtAuthenticationFilter(authenticationManager(http), jwtUtil);

        JwtAuthorizationFilter authorizationFilter =
                new JwtAuthorizationFilter(jwtUtil, userService);

        http
                .csrf(csrf -> csrf.disable())
                .authorizeHttpRequests(auth -> auth
                        .requestMatchers("/auth/login", "/public/**").permitAll()
                        .anyRequest().authenticated()
                )
                .addFilter(authFilter)
                .addFilterBefore(authorizationFilter, UsernamePasswordAuthenticationFilter.class);

        return http.build();
    }

    @Bean
    public AuthenticationManager authenticationManager(HttpSecurity http) throws Exception {
        return http.getSharedObject(AuthenticationManagerBuilder.class)
                .userDetailsService(userService)
                .passwordEncoder(new BCryptPasswordEncoder())
                .and()
                .build();
    }
}
```

---

# 9. CORS Configuration

```java
@Configuration
public class CorsConfig {

    @Bean
    public CorsConfigurationSource corsConfigurationSource() {
        CorsConfiguration config = new CorsConfiguration();
        config.addAllowedOriginPattern("*");
        config.addAllowedMethod("*");
        config.addAllowedHeader("*");
        config.setAllowCredentials(true);

        UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();
        source.registerCorsConfiguration("/**", config);

        return source;
    }
}
```

Explanation:

* Allows all origins
* Allows all HTTP methods
* Allows all headers
* Required for frontend apps like React, Angular, Vue

---

# 10. Method-Level Security

Enabled with:

```java
@EnableMethodSecurity
```

## 10.1 @PreAuthorize

Runs before the method executes.

Example:

```java
@PreAuthorize("hasRole('ADMIN')")
@GetMapping("/admin/data")
public String adminData() {
    return "Admin data";
}
```

More examples:

```java
@PreAuthorize("#id == authentication.principal.username")
@GetMapping("/user/{id}")
public String getUserData(String id) {
    return "User-specific data";
}
```

## 10.2 @PostAuthorize

Executes after method returns.

```java
@PostAuthorize("returnObject.username == authentication.name")
@GetMapping("/profile")
public User getProfile() {
    return userService.getCurrentUser();
}
```

Use case:

* Sensitive data that requires checking response before sending it

---

# 11. REST Endpoints Example

### Public Controller

```java
@RestController
@RequestMapping("/public")
public class PublicController {

    @GetMapping("/ping")
    public String ping() {
        return "Public API working";
    }
}
```

### User Controller

```java
@RestController
@RequestMapping("/user")
public class UserController {

    @PreAuthorize("hasRole('USER')")
    @GetMapping("/data")
    public String data() {
        return "User data accessed";
    }
}
```

### Admin Controller

```java
@RestController
@RequestMapping("/admin")
public class AdminController {

    @PreAuthorize("hasRole('ADMIN')")
    @GetMapping("/panel")
    public String adminPanel() {
        return "Admin panel";
    }
}
```

---

# 12. Testing JWT Flow

1. Send POST to `/auth/login` with username and password.
2. Server returns token.
3. Add token to headers:
   `Authorization: Bearer <token>`
4. Access secured APIs.
5. For restricted APIs, server returns 403 if role mismatch.
6. For invalid token, server returns 401.

---

# 13. Summary of What You Learned

You now understand:

1. JWT authentication flow
2. JWT generation and validation
3. Authentication and Authorization filters
4. Role-based access
5. Method-level security
6. CORS configuration
7. Protecting endpoints
8. Custom login and stateless session design

---
