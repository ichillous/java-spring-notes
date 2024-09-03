---

# ⚙️ H2 Configuration in Spring Boot

Configuring the H2 database in a Spring Boot application is straightforward and enables you to quickly set up an in-memory or file-based database for development, testing, or lightweight production use. Spring Boot provides auto-configuration options that simplify the integration and management of H2.

## 🛠️ Configuring H2 in Spring Boot

### 1. **Add H2 Dependency**

First, you need to include the H2 database dependency in your `pom.xml` file:

```xml
<dependencies>
    <dependency>
        <groupId>com.h2database</groupId>
        <artifactId>h2</artifactId>
        <version>2.1.214</version>
    </dependency>
</dependencies>
```

### 2. **Set Up Application Properties**

You can configure H2 using the `application.properties` or `application.yml` file, depending on whether you want to use an in-memory or file-based database.

#### **In-Memory Database Configuration**

An in-memory database is ideal for testing purposes because the data is not persisted to disk:

```properties
spring.datasource.url=jdbc:h2:mem:testdb
spring.datasource.driverClassName=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=
spring.h2.console.enabled=true
spring.h2.console.path=/h2-console
spring.jpa.database-platform=org.hibernate.dialect.H2Dialect
```

#### **File-Based Database Configuration**

A file-based database is useful when you need to persist data across application restarts:

```properties
spring.datasource.url=jdbc:h2:file:./data/testdb
spring.datasource.driverClassName=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=
spring.h2.console.enabled=true
spring.h2.console.path=/h2-console
spring.jpa.database-platform=org.hibernate.dialect.H2Dialect
```

### 3. **Enable H2 Console**

The H2 console is a web-based interface that allows you to interact with your database. You can enable it by setting `spring.h2.console.enabled` to `true` and specify the path where the console can be accessed.

```properties
spring.h2.console.enabled=true
spring.h2.console.path=/h2-console
```

Access the H2 console by navigating to `http://localhost:8080/h2-console` in your browser.

### 4. **JPA and Hibernate Configuration**

Spring Boot uses Hibernate as the default JPA provider, and you can configure it to work seamlessly with H2.

- **Automatic Schema Creation**: 
  You can use the `spring.jpa.hibernate.ddl-auto` property to control how the schema is created:

  ```properties
  spring.jpa.hibernate.ddl-auto=update
  ```

  This setting will automatically create or update the database schema based on your entity classes.

- **Show SQL Queries**: 
  Enable this option to log the SQL statements executed by Hibernate:

  ```properties
  spring.jpa.show-sql=true
  ```

### 5. **Creating Entities and Repositories**

Once H2 is configured, you can define your entities and repositories as you would with any other database.

**Example Entity:**

```java
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;

@Entity
public class User {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String username;
    private String email;

    // Getters and setters
}
```

**Example Repository:**

```java
import org.springframework.data.jpa.repository.JpaRepository;

public interface UserRepository extends JpaRepository<User, Long> {
}
```

### 6. **Testing with H2**

H2 is ideal for unit and integration testing in Spring Boot. It allows you to test your application’s data layer without requiring a full database setup.

**Example Test Configuration:**

```java
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

import java.util.List;

import static org.assertj.core.api.Assertions.assertThat;

@SpringBootTest
public class UserRepositoryTests {

    @Autowired
    private UserRepository userRepository;

    @Test
    public void testFindAll() {
        List<User> users = userRepository.findAll();
        assertThat(users).isNotNull();
    }
}
```

### 7. **Advanced H2 Configuration**

#### **Custom Database File Location**

You can specify a custom location for the H2 database file:

```properties
spring.datasource.url=jdbc:h2:file:/custom/path/to/db/testdb
```

#### **Mixed Mode Configuration**

In mixed mode, the database can be accessed both as an embedded database and as a server:

```properties
spring.datasource.url=jdbc:h2:file:~/testdb;AUTO_SERVER=TRUE
```

#### **Encryption**

You can enable database encryption to secure sensitive data:

```properties
spring.datasource.url=jdbc:h2:file:./data/testdb;CIPHER=AES
```

## 📜 Summary

Configuring H2 in Spring Boot is a simple process that provides a powerful tool for developing, testing, and even running small-scale production applications. Whether you use it as an in-memory database for testing or as a file-based database for persistence, H2's integration with Spring Boot offers flexibility and ease of use.

---
