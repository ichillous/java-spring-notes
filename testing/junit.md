---

# 🧪 Unit Testing with JUnit in Spring Boot

JUnit is the most popular framework for writing and running tests in the Java ecosystem. It provides a robust set of tools to test the functionality of your Spring Boot applications, ensuring that your code is reliable and bug-free.

## 🎯 Why Unit Testing?

- **Verifies Code Behavior**: Ensures that each unit of the code (like a method or class) works as expected.
- **Prevents Regression**: Helps catch bugs when the codebase changes.
- **Improves Code Quality**: Encourages writing modular and clean code.
- **Documentation**: Serves as documentation by showing how components are supposed to be used.

## 🛠️ Setting Up JUnit in Spring Boot

### 1. **Add JUnit Dependency**

Spring Boot includes JUnit 5 by default with the `spring-boot-starter-test` dependency, which is added automatically when you create a Spring Boot project using Spring Initializr.

**Dependency in `pom.xml`:**

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
</dependency>
```

### 2. **Project Structure for Tests**

Your tests should be placed under `src/test/java` and mirror the structure of your main source directory.

```
src/
 ├── main/
 │   └── java/
 │        └── com/example/demo/
 │             ├── DemoApplication.java
 │             ├── controller/
 │             ├── model/
 │             ├── repository/
 │             └── service/
 └── test/
      └── java/
           └── com/example/demo/
                ├── DemoApplicationTests.java
                ├── controller/
                ├── model/
                ├── repository/
                └── service/
```

## 📂 Writing Unit Tests with JUnit

### 1. **Basic Test Structure**

A JUnit test case is a method annotated with `@Test`. Each test should focus on a single "unit" of functionality.

**Example:**

```java
package com.example.demo.service;

import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.*;

class UserServiceTest {

    @Test
    void testUserCreation() {
        User user = new User("John", "john@example.com");
        assertNotNull(user);
        assertEquals("John", user.getName());
    }
}
```

### 2. **Testing Spring Components**

Spring Boot makes it easy to test components like services and controllers using dependency injection and context loading.

**Example: Testing a Service**

```java
package com.example.demo.service;

import com.example.demo.model.User;
import com.example.demo.repository.UserRepository;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

import static org.junit.jupiter.api.Assertions.*;

@SpringBootTest
class UserServiceTest {

    @Autowired
    private UserService userService;

    @Autowired
    private UserRepository userRepository;

    @Test
    void testFindUserById() {
        User user = new User("John", "john@example.com");
        userRepository.save(user);

        User foundUser = userService.getUserById(user.getId());
        assertEquals(user.getName(), foundUser.getName());
    }
}
```

### 3. **Mocking Dependencies with Mockito**

Mockito is a powerful mocking framework that allows you to create mock objects and define behavior for dependencies.

**Example:**

```java
package com.example.demo.service;

import com.example.demo.model.User;
import com.example.demo.repository.UserRepository;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;

import static org.mockito.Mockito.*;
import static org.junit.jupiter.api.Assertions.*;

class UserServiceTest {

    @Mock
    private UserRepository userRepository;

    @InjectMocks
    private UserService userService;

    public UserServiceTest() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    void testGetUserById() {
        User user = new User("John", "john@example.com");
        when(userRepository.findById(anyLong())).thenReturn(Optional.of(user));

        User foundUser = userService.getUserById(1L);
        assertEquals("John", foundUser.getName());
    }
}
```

### 4. **Parameterized Tests**

JUnit 5 supports parameterized tests, which allow you to run the same test multiple times with different parameters.

**Example:**

```java
import org.junit.jupiter.params.ParameterizedTest;
import org.junit.jupiter.params.provider.ValueSource;

import static org.junit.jupiter.api.Assertions.*;

class ParameterizedTestExample {

    @ParameterizedTest
    @ValueSource(strings = {"racecar", "radar", "level"})
    void testPalindrome(String word) {
        assertTrue(isPalindrome(word));
    }

    boolean isPalindrome(String word) {
        return new StringBuilder(word).reverse().toString().equals(word);
    }
}
```

## 🔑 Best Practices

- **Write Tests for All Critical Paths**: Ensure that you cover all critical paths, including edge cases.
- **Keep Tests Independent**: Tests should be independent of each other to ensure that one test’s failure doesn’t affect another.
- **Use Assertions Effectively**: Use a variety of assertions (`assertEquals`, `assertTrue`, `assertNotNull`, etc.) to validate different conditions.
- **Avoid Over-Mocking**: Mock only external dependencies, not the class under test.
- **Test Coverage**: Aim for high test coverage, but focus on quality over quantity.

## 📚 Further Reading

- [JUnit 5 User Guide](https://junit.org/junit5/docs/current/user-guide/)
- [Mockito Documentation](https://site.mockito.org/)
- [Testing Spring Boot Applications](https://spring.io/guides/gs/testing-web/)

---
