---

# 🏷️ Spring Boot Annotations

Spring Boot leverages annotations to simplify and streamline the configuration and setup of Spring applications. These annotations allow developers to define beans, handle dependency injection, configure components, and much more with minimal boilerplate code.

## 📌 Key Spring Boot Annotations

### 1. `@SpringBootApplication`

This is the central annotation for any Spring Boot application. It combines three commonly used annotations:

- `@Configuration`: Marks the class as a source of bean definitions.
- `@EnableAutoConfiguration`: Enables Spring Boot’s auto-configuration mechanism.
- `@ComponentScan`: Scans for components, configurations, and services in the specified package.

**Example:**

```java
@SpringBootApplication
public class MyApplication {
    public static void main(String[] args) {
        SpringApplication.run(MyApplication.class, args);
    }
}
```

### 2. `@RestController`

A specialized version of the `@Controller` annotation that combines `@Controller` and `@ResponseBody`. It is typically used in RESTful web services to handle HTTP requests.

**Example:**

```java
@RestController
public class MyController {

    @GetMapping("/hello")
    public String sayHello() {
        return "Hello, World!";
    }
}
```

### 3. `@RequestMapping`

Used to map HTTP requests to handler methods in controller classes. This annotation can be applied at both class and method levels.

**Example:**

```java
@RestController
@RequestMapping("/api")
public class ApiController {

    @GetMapping("/users")
    public List<User> getUsers() {
        return userService.getAllUsers();
    }
}
```

### 4. `@Autowired`

This annotation is used for automatic dependency injection. Spring will automatically inject the appropriate bean by type.

**Example:**

```java
@Service
public class MyService {

    @Autowired
    private MyRepository myRepository;

    // business logic here
}
```

### 5. `@Component`

This generic annotation is used to define a Spring-managed bean. It serves as a parent annotation for other specialized annotations like `@Service`, `@Repository`, and `@Controller`.

**Example:**

```java
@Component
public class MyComponent {

    public void doSomething() {
        // logic here
    }
}
```

### 6. `@Value`

Used to inject values into fields, methods, or constructor arguments from a property file or system properties.

**Example:**

```java
@Component
public class MyComponent {

    @Value("${app.name}")
    private String appName;

    public String getAppName() {
        return appName;
    }
}
```

### 7. `@Configuration`

Indicates that a class declares one or more `@Bean` methods and may be processed by the Spring container to generate bean definitions and service requests.

**Example:**

```java
@Configuration
public class AppConfig {

    @Bean
    public MyService myService() {
        return new MyServiceImpl();
    }
}
```

### 8. `@EnableAutoConfiguration`

This annotation tells Spring Boot to start adding beans based on classpath settings, other beans, and various property settings. It’s typically used in conjunction with `@SpringBootApplication`.

**Example:**

```java
@EnableAutoConfiguration
public class MyApplication {
    public static void main(String[] args) {
        SpringApplication.run(MyApplication.class, args);
    }
}
```

### 9. `@ConditionalOnProperty`

Used to conditionally enable a component based on the presence of a property in the `application.properties` or `application.yml`.

**Example:**

```java
@Configuration
@ConditionalOnProperty(name = "feature.enabled", havingValue = "true")
public class FeatureConfig {

    @Bean
    public FeatureService featureService() {
        return new FeatureService();
    }
}
```

## 📜 Summary

Spring Boot annotations play a crucial role in reducing boilerplate code and simplifying the configuration and development of Spring-based applications. By understanding and using these annotations effectively, you can take full advantage of Spring Boot’s capabilities to build robust and scalable applications.

---
