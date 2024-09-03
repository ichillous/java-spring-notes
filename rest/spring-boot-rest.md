---

# 🚀 Building RESTful APIs with Spring Boot

Spring Boot makes it easy to create stand-alone, production-grade Spring-based applications that you can "just run." One of its most powerful features is the ability to build RESTful web services quickly and easily.

## 📦 Setting Up a Spring Boot Project

### 1. **Using Spring Initializr**

Spring Initializr is a web-based tool that helps you bootstrap your Spring Boot project with necessary dependencies.

- Visit [Spring Initializr](https://start.spring.io/).
- Select your project metadata (Maven/Gradle, Java version, etc.).
- Choose dependencies like `Spring Web`, `Spring Data JPA`, and `MySQL`.
- Click "Generate" to download your project as a zip file.
- Unzip and import the project into your IDE.

### 2. **Project Structure**

After setting up, your project structure will look something like this:

```
src/
 ├── main/
 │   ├── java/
 │   │   └── com/example/demo/
 │   │        ├── DemoApplication.java
 │   │        ├── controller/
 │   │        ├── model/
 │   │        ├── repository/
 │   │        └── service/
 │   └── resources/
 │        ├── application.properties
 │        └── static/
 └── test/
      └── java/
```

## 🛠️ Creating RESTful Endpoints

### 1. **Defining a Model**

A model represents the data in your application. In a RESTful API, it typically maps to a database table.

**Example:**

```java
package com.example.demo.model;

import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;

@Entity
public class User {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;
    private String email;

    // Getters and Setters
}
```

### 2. **Creating a Repository**

The repository layer is responsible for interacting with the database. Spring Data JPA provides a convenient interface for CRUD operations.

**Example:**

```java
package com.example.demo.repository;

import com.example.demo.model.User;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

@Repository
public interface UserRepository extends JpaRepository<User, Long> {
}
```

### 3. **Building a Service Layer**

The service layer contains business logic. It interacts with the repository and is typically called by the controller.

**Example:**

```java
package com.example.demo.service;

import com.example.demo.model.User;
import com.example.demo.repository.UserRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.List;

@Service
public class UserService {

    @Autowired
    private UserRepository userRepository;

    public List<User> getAllUsers() {
        return userRepository.findAll();
    }

    public User getUserById(Long id) {
        return userRepository.findById(id).orElse(null);
    }

    public User saveUser(User user) {
        return userRepository.save(user);
    }

    public void deleteUser(Long id) {
        userRepository.deleteById(id);
    }
}
```

### 4. **Creating a REST Controller**

The controller layer handles HTTP requests and responses. It’s where you define the RESTful endpoints.

**Example:**

```java
package com.example.demo.controller;

import com.example.demo.model.User;
import com.example.demo.service.UserService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;

import java.util.List;

@RestController
@RequestMapping("/api/users")
public class UserController {

    @Autowired
    private UserService userService;

    @GetMapping
    public List<User> getAllUsers() {
        return userService.getAllUsers();
    }

    @GetMapping("/{id}")
    public User getUserById(@PathVariable Long id) {
        return userService.getUserById(id);
    }

    @PostMapping
    public User createUser(@RequestBody User user) {
        return userService.saveUser(user);
    }

    @PutMapping("/{id}")
    public User updateUser(@PathVariable Long id, @RequestBody User user) {
        user.setId(id);
        return userService.saveUser(user);
    }

    @DeleteMapping("/{id}")
    public void deleteUser(@PathVariable Long id) {
        userService.deleteUser(id);
    }
}
```

## 🔐 Handling Security

To secure your RESTful API, Spring Security can be integrated to handle authentication and authorization.

### 1. **Add Spring Security Dependency**

Add the following dependency to your `pom.xml`:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

### 2. **Basic Security Configuration**

**Example:**

```java
package com.example.demo.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;
import org.springframework.security.crypto.password.PasswordEncoder;

@Configuration
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
            .csrf().disable()
            .authorizeRequests()
            .antMatchers("/api/users/**").authenticated()
            .and()
            .httpBasic();
    }

    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }
}
```

## 🧪 Testing Your API

Spring Boot makes testing easy with built-in support for JUnit and MockMvc.

### 1. **Testing with MockMvc**

**Example:**

```java
package com.example.demo;

import com.example.demo.controller.UserController;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest;
import org.springframework.test.web.servlet.MockMvc;

import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.get;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;

@WebMvcTest(UserController.class)
public class UserControllerTest {

    @Autowired
    private MockMvc mockMvc;

    @Test
    public void shouldReturnAllUsers() throws Exception {
        mockMvc.perform(get("/api/users"))
                .andExpect(status().isOk());
    }
}
```

## 🔑 Best Practices

- **Use DTOs**: Use Data Transfer Objects (DTOs) to expose only necessary data to the client.
- **Exception Handling**: Implement global exception handling using `@ControllerAdvice`.
- **Input Validation**: Use `@Valid` and validation annotations to validate incoming requests.
- **Use HATEOAS**: Implement Hypermedia as the Engine of Application State to make your REST APIs more discoverable.
- **Documentation**: Use Swagger/OpenAPI for documenting your RESTful services.

## 📚 Further Reading

- [Spring Boot Official Documentation](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/)
- [Building RESTful Web Services with Spring](https://spring.io/guides/tutorials/rest/)
- [Spring Security Guide](https://spring.io/guides/gs/securing-web/)

---
