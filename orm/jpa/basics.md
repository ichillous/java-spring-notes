---

# 🗄️ JPA Basics

Java Persistence API (JPA) is a specification that provides an object-relational mapping (ORM) framework to manage relational data in Java applications. JPA simplifies database interactions by allowing developers to work with objects and classes rather than SQL statements. Spring Data JPA is a part of the Spring framework that helps implement JPA in Spring applications with minimal boilerplate code.

## 🛠️ What is JPA?

JPA is a standard for ORM in Java that defines a set of rules and guidelines for managing data between Java objects and a relational database. It provides an abstraction layer that allows developers to interact with the database using Java objects, reducing the need for extensive SQL queries.

### 📘 Key Concepts in JPA

1. **Entity:**
   - An entity represents a table in the database, and each instance of an entity corresponds to a row in that table.
   - Entities are defined using the `@Entity` annotation.

   **Example:**
   ```java
   import javax.persistence.Entity;
   import javax.persistence.Id;

   @Entity
   public class User {
       @Id
       private Long id;
       private String username;
       private String email;
       // Getters and setters
   }
   ```

2. **Primary Key:**
   - The primary key is a unique identifier for each entity instance. It is typically annotated with `@Id`.
   - You can generate primary keys automatically using `@GeneratedValue`.

   **Example:**
   ```java
   @Id
   @GeneratedValue(strategy = GenerationType.IDENTITY)
   private Long id;
   ```

3. **Persistence Context:**
   - The persistence context is a set of entity instances where for any persistent entity identity, there is a unique entity instance.
   - Managed by an `EntityManager`, the persistence context handles the lifecycle of entities, including creating, updating, and deleting records in the database.

4. **EntityManager:**
   - The `EntityManager` is the primary JPA interface for interacting with the persistence context.
   - It provides methods for querying and managing entities, such as `persist`, `find`, `merge`, and `remove`.

   **Example:**
   ```java
   @PersistenceContext
   private EntityManager entityManager;

   public void saveUser(User user) {
       entityManager.persist(user);
   }
   ```

5. **JPQL (Java Persistence Query Language):**
   - JPQL is a query language similar to SQL but operates on entity objects rather than database tables.
   - It allows you to perform queries on your entities using a more object-oriented syntax.

   **Example:**
   ```java
   String jpql = "SELECT u FROM User u WHERE u.username = :username";
   User user = entityManager.createQuery(jpql, User.class)
                            .setParameter("username", "john_doe")
                            .getSingleResult();
   ```

6. **Annotations:**
   - JPA uses a variety of annotations to map Java objects to database tables, columns, relationships, and more.

   **Common Annotations:**
   - `@Entity`: Marks a class as an entity.
   - `@Table`: Specifies the table name (optional if the class name matches the table name).
   - `@Id`: Marks the primary key field.
   - `@GeneratedValue`: Specifies how the primary key should be generated.
   - `@Column`: Customizes the mapping between the field and the database column.
   - `@OneToOne`, `@OneToMany`, `@ManyToOne`, `@ManyToMany`: Define relationships between entities.

### 🔄 Lifecycle of an Entity

1. **New (Transient):**
   - The entity is created but not associated with a persistence context. It has no representation in the database.

2. **Managed (Persistent):**
   - The entity is associated with a persistence context and will be saved in the database during a transaction.

3. **Detached:**
   - The entity is no longer associated with a persistence context, typically after the transaction is committed.

4. **Removed:**
   - The entity is marked for removal and will be deleted from the database upon transaction commit.

### 🧩 Relationships Between Entities

JPA allows you to define relationships between entities using annotations:

- **One-to-One Relationship:**
  ```java
  @OneToOne
  @JoinColumn(name = "profile_id")
  private Profile profile;
  ```

- **One-to-Many Relationship:**
  ```java
  @OneToMany(mappedBy = "user")
  private List<Order> orders;
  ```

- **Many-to-One Relationship:**
  ```java
  @ManyToOne
  @JoinColumn(name = "user_id")
  private User user;
  ```

- **Many-to-Many Relationship:**
  ```java
  @ManyToMany
  @JoinTable(
      name = "user_roles",
      joinColumns = @JoinColumn(name = "user_id"),
      inverseJoinColumns = @JoinColumn(name = "role_id"))
  private Set<Role> roles;
  ```

### 🔧 Advanced JPA Features

1. **Inheritance Mapping:**
   - JPA supports inheritance among entities, allowing you to map a class hierarchy to database tables.

   **Example:**
   ```java
   @Entity
   @Inheritance(strategy = InheritanceType.JOINED)
   public class Person {
       @Id
       @GeneratedValue(strategy = GenerationType.IDENTITY)
       private Long id;
       private String name;
   }

   @Entity
   public class Employee extends Person {
       private String department;
   }
   ```

2. **Cascading:**
   - Cascading allows operations to be propagated from a parent entity to related entities. Common cascade types include `PERSIST`, `MERGE`, `REMOVE`, `REFRESH`, and `DETACH`.

   **Example:**
   ```java
   @OneToMany(cascade = CascadeType.ALL, orphanRemoval = true)
   private List<Order> orders;
   ```

3. **Fetch Strategies:**
   - JPA provides two fetch types: `EAGER` (immediate loading) and `LAZY` (on-demand loading).

   **Example:**
   ```java
   @ManyToOne(fetch = FetchType.LAZY)
   private User user;
   ```

## 📜 Summary

JPA is a powerful tool for managing relational data in Java applications, providing a way to map Java objects to database tables seamlessly. Understanding the basics of entities, relationships, JPQL, and annotations is essential for effectively using JPA in your Spring Boot projects. By leveraging JPA, you can simplify data management, reduce boilerplate code, and focus more on your application's business logic.

---
