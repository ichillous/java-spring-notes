# Spring Framework: Core Concepts

## Table of Contents
1. [Introduction to Spring Framework](#introduction-to-spring-framework)
2. [Inversion of Control (IoC)](#inversion-of-control-ioc)
3. [Dependency Injection (DI)](#dependency-injection-di)
4. [Spring IoC Container](#spring-ioc-container)
5. [Beans](#beans)
6. [Bean Scopes](#bean-scopes)
7. [Bean Lifecycle](#bean-lifecycle)
8. [Configuration Styles](#configuration-styles)
9. [Annotations](#annotations)
10. [Aspect-Oriented Programming (AOP)](#aspect-oriented-programming-aop)
11. [Spring Expression Language (SpEL)](#spring-expression-language-spel)
12. [Validation, Data Binding, and Type Conversion](#validation-data-binding-and-type-conversion)

## Introduction to Spring Framework

The Spring Framework is an open-source application framework and inversion of control container for the Java platform. It provides comprehensive infrastructure support for developing Java applications, promoting good programming practices by utilizing design patterns like Dependency Injection (DI) and Aspect-Oriented Programming (AOP).

Key features of Spring Framework:
- Lightweight and minimally invasive development with Plain Old Java Objects (POJOs)
- Dependency injection to promote loose coupling
- Declarative programming with Aspect-Oriented Programming (AOP)
- Reduction of boilerplate code

## Inversion of Control (IoC)

Inversion of Control is a principle in software engineering where the control of objects or portions of a program is transferred to a container or framework. It's a broad term that includes, but is not limited to, Dependency Injection.

In traditional programming, the custom code that expresses the purpose of the program calls into reusable libraries to take care of generic tasks. With IoC, it's the framework that calls into the custom, or task-specific, code.

Benefits of IoC:
- Decoupling the execution of a task from its implementation
- Making it easier to switch between different implementations
- Greater modularity of a program
- Greater ease in testing a program by isolating components

## Dependency Injection (DI)

Dependency Injection is a specific form of IoC where the creation and binding of dependent objects are separated from the class that depends on them. Instead of a class creating its dependencies, they are "injected" into the class from the outside.

There are three common types of dependency injection:

1. Constructor Injection
2. Setter Injection
3. Field Injection

Example of Constructor Injection:

```java
public class UserService {
    private final UserRepository userRepository;

    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }
}
```

Benefits of DI:
- Reduced coupling between classes
- Increased modularity
- Easier unit testing with mock objects

## Spring IoC Container

The Spring IoC Container is the core of the Spring Framework. It creates objects, configures them, and manages their complete lifecycle. The container uses Dependency Injection to manage the components that make up an application.

There are two types of IoC containers in Spring:

1. BeanFactory: This is the simplest container, providing basic support for DI. It's mostly used in light-weight applications.

2. ApplicationContext: This is an advanced container which includes all functionality of BeanFactory and adds more enterprise-specific functionality. It's the preferred container.

Example of creating an ApplicationContext:

```java
ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
// or
ApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class);
```

## Beans

In Spring, the objects that form the backbone of your application and that are managed by the Spring IoC container are called beans. A bean is an object that is instantiated, assembled, and otherwise managed by a Spring IoC container.

Beans are created with the configuration metadata that you supply to the container, for example, in the form of XML `<bean/>` definitions or Java annotations.

Example of a bean definition in XML:

```xml
<bean id="userService" class="com.example.UserService">
    <constructor-arg ref="userRepository" />
</bean>
```

Example of a bean definition using Java annotation:

```java
@Component
public class UserService {
    // ...
}
```

## Bean Scopes

Spring Beans can be defined as having one of the following scopes:

1. singleton (default): One instance of the bean is created for the entire application
2. prototype: A new instance is created every time the bean is requested
3. request: One instance per HTTP request (only valid in web-aware Spring ApplicationContext)
4. session: One instance per HTTP session (only valid in web-aware Spring ApplicationContext)
5. application: One instance per ServletContext (only valid in web-aware Spring ApplicationContext)
6. websocket: One instance per WebSocket session (only valid in web-aware Spring ApplicationContext)

Example of defining a prototype bean:

```java
@Component
@Scope("prototype")
public class PrototypeBean {
    // ...
}
```

## Bean Lifecycle

Spring manages the lifecycle of a bean. The basic lifecycle of a bean is:

1. Instantiate
2. Populate Properties
3. Call setBeanName method
4. Call setBeanFactory method
5. Call setApplicationContext method
6. PreInitialization (BeanPostProcessors)
7. AfterPropertiesSet method
8. Custom Init method
9. PostInitialization (BeanPostProcessors)
10. Bean is ready to use
11. Container is shut down
12. DisposableBean's destroy method
13. Custom Destroy method

You can hook into this lifecycle using various annotations or XML configurations:

```java
@Component
public class MyBean implements InitializingBean, DisposableBean {

    @PostConstruct
    public void postConstruct() {
        System.out.println("PostConstruct method called");
    }

    @Override
    public void afterPropertiesSet() throws Exception {
        System.out.println("AfterPropertiesSet method called");
    }

    @PreDestroy
    public void preDestroy() {
        System.out.println("PreDestroy method called");
    }

    @Override
    public void destroy() throws Exception {
        System.out.println("DisposableBean's Destroy method called");
    }
}
```

## Configuration Styles

Spring supports several styles of configuration:

1. XML-based configuration:
   
   ```xml
   <beans>
       <bean id="userService" class="com.example.UserService">
           <constructor-arg ref="userRepository" />
       </bean>
   </beans>
   ```

2. Annotation-based configuration:
   
   ```java
   @Configuration
   public class AppConfig {
       @Bean
       public UserService userService(UserRepository userRepository) {
           return new UserService(userRepository);
       }
   }
   ```

3. Java-based configuration:
   
   ```java
   @Configuration
   public class AppConfig {
       @Bean
       public UserService userService() {
           return new UserService(userRepository());
       }

       @Bean
       public UserRepository userRepository() {
           return new JpaUserRepository();
       }
   }
   ```

4. Groovy Bean Definition DSL:
   
   ```groovy
   beans {
       userRepository(JpaUserRepository)
       userService(UserService, ref('userRepository'))
   }
   ```

## Annotations

Spring provides a wide range of annotations to simplify configuration and reduce boilerplate code. Some of the most commonly used annotations include:

- `@Component`: Indicates that an annotated class is a "component" and should be auto-detected for dependency injection.
- `@Autowired`: Marks a constructor, field, setter method, or config method to be autowired by Spring's dependency injection facilities.
- `@Configuration`: Indicates that a class declares one or more `@Bean` methods and may be processed by the Spring container to generate bean definitions.
- `@Bean`: Indicates that a method produces a bean to be managed by the Spring container.
- `@Value`: Indicates that an annotated member should be injected with an externalized property value.
- `@PropertySource`: Provides a convenient and declarative mechanism for adding a PropertySource to Spring's Environment.

Example:

```java
@Configuration
@PropertySource("classpath:application.properties")
public class AppConfig {

    @Value("${database.url}")
    private String databaseUrl;

    @Bean
    public DataSource dataSource() {
        // create and configure DataSource
    }
}
```

## Aspect-Oriented Programming (AOP)

AOP is a programming paradigm that aims to increase modularity by allowing the separation of cross-cutting concerns. It does this by adding additional behavior to existing code without modifying the code itself.

Key AOP concepts:
- Aspect: A modularization of a concern that cuts across multiple classes.
- Join point: A point during the execution of a program, such as the execution of a method or the handling of an exception.
- Advice: Action taken by an aspect at a particular join point.
- Pointcut: A predicate that matches join points.

Example of an aspect in Spring:

```java
@Aspect
@Component
public class LoggingAspect {

    @Before("execution(* com.example.service.*.*(..))")
    public void logBefore(JoinPoint joinPoint) {
        System.out.println("Before method: " + joinPoint.getSignature().getName());
    }
}
```

## Spring Expression Language (SpEL)

SpEL is a powerful expression language that supports querying and manipulating an object graph at runtime. It can be used with XML or annotation-based configurations.

Features of SpEL:
- Literal expressions
- Boolean and relational operators
- Regular expressions
- Class expressions
- Accessing properties, arrays, lists, maps
- Method invocation
- Relational operators
- Assignment
- Calling constructors
- Bean references
- Array construction

Example:

```java
@Value("#{systemProperties['user.region']}")
private String region;

@Value("#{T(java.lang.Math).random() * 100.0}")
private double randomNumber;
```

## Validation, Data Binding, and Type Conversion

Spring provides a Validator interface that you can use to validate objects. The DataBinder is responsible for binding user input to target objects and can be configured with custom type converters and validators.

Example of a custom validator:

```java
public class PersonValidator implements Validator {

    @Override
    public boolean supports(Class<?> clazz) {
        return Person.class.isAssignableFrom(clazz);
    }

    @Override
    public void validate(Object target, Errors errors) {
        Person person = (Person) target;
        if (person.getName().isEmpty()) {
            errors.rejectValue("name", "name.empty");
        }
        // more validation...
    }
}
```

Spring also supports JSR-303/JSR-349 Bean Validation via LocalValidatorFactoryBean, which adapts Spring's Validator interface to the Bean Validation API.

```java
public class Person {

    @NotNull
    @Size(min=2, max=30)
    private String name;

    @Min(18)
    private int age;

    // getters and setters...
}
```

These core concepts form the foundation of the Spring Framework. Understanding these will greatly aid in effectively using Spring and its various modules.
