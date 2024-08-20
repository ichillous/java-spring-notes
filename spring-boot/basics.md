# Spring Boot Basics

## Table of Contents
1. [Introduction](#introduction)
2. [Key Concepts](#key-concepts)
   - [Auto-configuration](#auto-configuration)
   - [Opinionated Defaults](#opinionated-defaults)
   - [Standalone Applications](#standalone-applications)
3. [Getting Started](#getting-started)
   - [Setting Up a Spring Boot Project](#setting-up-a-spring-boot-project)
   - [Project Structure](#project-structure)
4. [Spring Boot Annotations](#spring-boot-annotations)
5. [Configuration](#configuration)
   - [Application Properties](#application-properties)
   - [YAML Configuration](#yaml-configuration)
6. [Spring Boot Starters](#spring-boot-starters)
7. [Embedded Servers](#embedded-servers)
8. [Actuator](#actuator)
9. [Best Practices and Common Pitfalls](#best-practices-and-common-pitfalls)
10. [Advanced Concepts](#advanced-concepts)
11. [Official Documentation and Resources](#official-documentation-and-resources)

## Introduction

Spring Boot is a project within the Spring ecosystem that simplifies the process of building production-ready applications. It takes an opinionated view of the Spring platform and third-party libraries, allowing developers to get started with minimum fuss.

The main goals of Spring Boot are to:
- Provide a radically faster and widely accessible getting-started experience for all Spring development.
- Be opinionated out of the box but get out of the way quickly as requirements start to diverge from the defaults.
- Provide a range of non-functional features that are common to large classes of projects (e.g., embedded servers, security, metrics, health checks, externalized configuration).
- Absolutely no code generation and no requirement for XML configuration.

## Key Concepts

### Auto-configuration

Spring Boot auto-configuration attempts to automatically configure your Spring application based on the jar dependencies that you have added. For example, if HSQLDB is on your classpath, and you have not manually configured any database connection beans, then Spring Boot will auto-configure an in-memory database.

```java
@SpringBootApplication
public class MyApplication {
    public static void main(String[] args) {
        SpringApplication.run(MyApplication.class, args);
    }
}
```

The `@SpringBootApplication` annotation enables auto-configuration and component scanning.

### Opinionated Defaults

Spring Boot provides opinionated defaults for various aspects of application development. These defaults are based on Spring's experience and best practices. However, you can easily override these defaults when needed.

### Standalone Applications

Spring Boot allows you to create standalone applications that can be run directly using Java's `main` method. It packages all dependencies, including an embedded web server, into a single, runnable JAR file.

## Getting Started

### Setting Up a Spring Boot Project

You can set up a Spring Boot project using:
1. Spring Initializr (https://start.spring.io/)
2. Your IDE's Spring Boot project creation wizard
3. Manual setup using Maven or Gradle

### Project Structure

A typical Spring Boot project structure looks like this:

```
my-project/
├── src/
│   ├── main/
│   │   ├── java/
│   │   │   └── com/
│   │   │       └── example/
│   │   │           └── myproject/
│   │   │               └── MyProjectApplication.java
│   │   └── resources/
│   │       └── application.properties
│   └── test/
│       └── java/
│           └── com/
│               └── example/
│                   └── myproject/
│                       └── MyProjectApplicationTests.java
├── pom.xml
└── README.md
```

## Spring Boot Annotations

Spring Boot introduces several useful annotations:

- `@SpringBootApplication`: Combines `@Configuration`, `@EnableAutoConfiguration`, and `@ComponentScan`
- `@EnableAutoConfiguration`: Enables Spring Boot's auto-configuration mechanism
- `@ConfigurationProperties`: Binds and validates external configurations to beans

Example:

```java
@SpringBootApplication
public class MyApplication {
    public static void main(String[] args) {
        SpringApplication.run(MyApplication.class, args);
    }
}
```

## Configuration

### Application Properties

Spring Boot allows you to externalize your configuration using `application.properties` or `application.yml` files.

Example `application.properties`:

```properties
server.port=8080
spring.datasource.url=jdbc:mysql://localhost/test
spring.datasource.username=dbuser
spring.datasource.password=dbpass
```

### YAML Configuration

YAML is a superset of JSON and is a more readable alternative to properties files.

Example `application.yml`:

```yaml
server:
  port: 8080
spring:
  datasource:
    url: jdbc:mysql://localhost/test
    username: dbuser
    password: dbpass
```

## Spring Boot Starters

Starters are a set of convenient dependency descriptors that you can include in your application. They greatly simplify your Maven configuration.

Common starters include:
- `spring-boot-starter-web`: For building web applications
- `spring-boot-starter-data-jpa`: For using Spring Data JPA with Hibernate
- `spring-boot-starter-security`: For adding Spring Security

To use a starter, add it to your `pom.xml`:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

## Embedded Servers

Spring Boot includes support for embedded servers, including Tomcat, Jetty, and Undertow. This means you can run your application as a standalone JAR without deploying to an external server.

The default embedded server is Tomcat. To switch to Jetty, for example:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
    <exclusions>
        <exclusion>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-tomcat</artifactId>
        </exclusion>
    </exclusions>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-jetty</artifactId>
</dependency>
```

## Actuator

Spring Boot Actuator provides production-ready features to help you monitor and manage your application.

To enable Actuator, add the following dependency:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

Actuator endpoints include:
- `/actuator/health`: Shows application health information
- `/actuator/info`: Displays arbitrary application info
- `/actuator/metrics`: Shows metrics information for the current application

## Best Practices and Common Pitfalls

1. Use the `@ConfigurationProperties` annotation for type-safe configuration.
2. Avoid using the `default` package for your application code.
3. Be cautious with `@ComponentScan` without any arguments, as it can slow down your application startup.
4. Use profiles for environment-specific configurations.
5. Be aware of the differences between `application.properties` and `bootstrap.properties`.

Common pitfalls:
- Forgetting to exclude test dependencies from the final JAR
- Misunderstanding the precedence of different configuration sources
- Overusing auto-configuration without understanding its implications

## Advanced Concepts

1. Custom Auto-configuration: Create your own auto-configuration for project-specific libraries or frameworks.
2. Spring Boot DevTools: Enhance your development experience with features like automatic restarts and live reload.
3. Customizing the Banner: Create a custom banner for your application's startup.
4. Externalized Configuration: Use `@Value` and `@ConfigurationProperties` for flexible configuration management.

## Official Documentation and Resources

- [Spring Boot Reference Documentation](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/)
- [Spring Boot Guides](https://spring.io/guides#gs)
- [Spring Boot API Documentation](https://docs.spring.io/spring-boot/docs/current/api/)
- [Spring Boot GitHub Repository](https://github.com/spring-projects/spring-boot)

Remember, Spring Boot is designed to get you up and running as quickly as possible, with minimal upfront configuration. As you become more comfortable with the framework, you can dive deeper into its advanced features and customization options.
