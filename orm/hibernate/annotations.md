---

# 📝 Hibernate Annotations

Hibernate annotations provide a way to define mapping metadata directly in the Java classes using Java's annotation feature. This approach is more concise and avoids the need for separate XML mapping files, making the code easier to manage and maintain.

## 🚀 Why Use Annotations?

- **Simplifies Configuration**: Reduces the need for verbose XML configuration files.
- **Improves Readability**: Keeps mapping information close to the code it relates to.
- **Flexibility**: Supports a wide range of annotations to cover various mapping scenarios.

## 🗂️ Common Hibernate Annotations

### 1. **@Entity**

Marks a class as a Hibernate entity (a persistent class). The class must have a no-argument constructor and should not be final.

**Example:**

```java
import javax.persistence.Entity;

@Entity
public class User {
    // Class contents
}
```

### 2. **@Table**

Specifies the table in the database that this entity maps to. By default, Hibernate uses the class name as the table name.

**Example:**

```java
import javax.persistence.Entity;
import javax.persistence.Table;

@Entity
@Table(name = "users")
public class User {
    // Class contents
}
```

### 3. **@Id**

Defines the primary key of the entity. Every entity must have a primary key, which can be a simple or composite key.

**Example:**

```java
import javax.persistence.Entity;
import javax.persistence.Id;

@Entity
public class User {

    @Id
    private Long id;

    // Other fields, getters, and setters
}
```

### 4. **@GeneratedValue**

Specifies the strategy for generating primary key values. Common strategies include `AUTO`, `IDENTITY`, `SEQUENCE`, and `TABLE`.

**Example:**

```java
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;

@Entity
public class User {

    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private Long id;

    // Other fields, getters, and setters
}
```

### 5. **@Column**

Maps a class field to a table column. You can specify column properties like name, length, nullable, etc.

**Example:**

```java
import javax.persistence.Column;
import javax.persistence.Entity;

@Entity
public class User {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(name = "user_name", nullable = false, length = 100)
    private String username;

    // Other fields, getters, and setters
}
```

### 6. **@Temporal**

Used with `java.util.Date` or `java.util.Calendar` fields to specify the exact temporal type (DATE, TIME, TIMESTAMP) that should be used in the database.

**Example:**

```java
import javax.persistence.Temporal;
import javax.persistence.TemporalType;
import java.util.Date;

@Entity
public class User {

    @Temporal(TemporalType.DATE)
    private Date birthDate;

    // Other fields, getters, and setters
}
```

### 7. **@Enumerated**

Maps an enumerated type (`enum`) to a database column. You can specify whether the value should be stored as an ordinal (integer) or as a string.

**Example:**

```java
import javax.persistence.Enumerated;
import javax.persistence.EnumType;

@Entity
public class User {

    public enum Role {
        ADMIN, USER, GUEST
    }

    @Enumerated(EnumType.STRING)
    private Role role;

    // Other fields, getters, and setters
}
```

### 8. **@OneToOne, @OneToMany, @ManyToOne, @ManyToMany**

Defines relationships between entities. These annotations specify how entities are related to each other, whether it's one-to-one, one-to-many, many-to-one, or many-to-many.

**Example: One-to-Many Relationship**

```java
import javax.persistence.*;
import java.util.Set;

@Entity
public class User {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @OneToMany(mappedBy = "user")
    private Set<Order> orders;

    // Other fields, getters, and setters
}
```

## 🔑 Best Practices

- **Use `@EntityListeners` for Lifecycle Events**: To handle entity lifecycle events such as `@PrePersist` and `@PostLoad`.
- **Favor Annotations Over XML**: Annotations are less error-prone and easier to maintain compared to XML mappings.
- **Keep Mapping Simple**: Avoid complex mapping structures unless necessary for your application.
- **Leverage `@Embeddable` for Reusable Components**: Use the `@Embeddable` annotation to create reusable components in your entities.

## 📚 Further Reading

- [Hibernate Annotations Reference](https://hibernate.org/orm/documentation/annotations/)
- [JPA and Hibernate Annotations](https://www.baeldung.com/jpa-annotations)
- [Spring Boot with JPA/Hibernate](https://spring.io/guides/gs/accessing-data-jpa/)

---
