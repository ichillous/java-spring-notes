---

# 📄 API Documentation with Swagger

Swagger is a powerful tool for designing, building, and documenting RESTful APIs. When integrated with Spring Boot, it provides a seamless way to generate interactive API documentation that can be easily shared and consumed by developers.

## 🌟 What is Swagger?

Swagger, now known as the OpenAPI Specification (OAS), is a framework for API documentation that enables you to describe the structure of your APIs so that machines and humans can easily understand and interact with them.

## 🛠️ Setting Up Swagger in Spring Boot

### 1. **Add Swagger Dependencies**

To get started with Swagger in a Spring Boot project, add the following dependencies to your `pom.xml`:

```xml
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-boot-starter</artifactId>
    <version>3.0.0</version>
</dependency>
```

### 2. **Basic Swagger Configuration**

Create a configuration class to customize the Swagger documentation for your API.

**Example:**

```java
package com.example.demo.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import springfox.documentation.builders.PathSelectors;
import springfox.documentation.builders.RequestHandlerSelectors;
import springfox.documentation.spi.DocumentationType;
import springfox.documentation.spring.web.plugins.Docket;
import springfox.documentation.swagger2.annotations.EnableSwagger2;

@Configuration
@EnableSwagger2
public class SwaggerConfig {

    @Bean
    public Docket api() {
        return new Docket(DocumentationType.SWAGGER_2)
                .select()
                .apis(RequestHandlerSelectors.basePackage("com.example.demo.controller"))
                .paths(PathSelectors.any())
                .build();
    }
}
```

### 3. **Accessing Swagger UI**

Once you have set up Swagger, you can access the interactive API documentation by navigating to the following URL in your web browser:

```
http://localhost:8080/swagger-ui/
```

This will bring up a user-friendly interface where you can explore and interact with your API endpoints.

## 🧩 Customizing Swagger Documentation

Swagger allows you to customize the generated documentation by adding metadata, descriptions, and tags.

### 1. **Adding API Metadata**

You can add general information about your API such as title, description, version, and contact information.

**Example:**

```java
import springfox.documentation.builders.ApiInfoBuilder;
import springfox.documentation.service.ApiInfo;
import springfox.documentation.service.Contact;

@Bean
public Docket api() {
    return new Docket(DocumentationType.SWAGGER_2)
            .select()
            .apis(RequestHandlerSelectors.basePackage("com.example.demo.controller"))
            .paths(PathSelectors.any())
            .build()
            .apiInfo(apiInfo());
}

private ApiInfo apiInfo() {
    return new ApiInfoBuilder()
            .title("My API")
            .description("API for managing users")
            .version("1.0.0")
            .contact(new Contact("Your Name", "www.example.com", "email@example.com"))
            .build();
}
```

### 2. **Annotating Controllers and Models**

You can use Swagger annotations to enhance the documentation generated for your controllers and models.

- **`@Api`**: Describes a controller.
- **`@ApiOperation`**: Describes a method in the controller.
- **`@ApiParam`**: Describes a parameter to a method.
- **`@ApiModel`**: Describes a model object.
- **`@ApiModelProperty`**: Describes a property in a model object.

**Example:**

```java
import io.swagger.annotations.Api;
import io.swagger.annotations.ApiOperation;
import io.swagger.annotations.ApiParam;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/api/users")
@Api(value = "User Management System", description = "Operations pertaining to users in the User Management System")
public class UserController {

    @ApiOperation(value = "View a list of available users", response = List.class)
    @GetMapping
    public List<User> getAllUsers() {
        // Implementation
    }

    @ApiOperation(value = "Get a user by Id")
    @GetMapping("/{id}")
    public User getUserById(
            @ApiParam(value = "ID of the user to retrieve", required = true) @PathVariable Long id) {
        // Implementation
    }
}
```

### 3. **Grouping APIs**

You can organize your APIs into groups for better navigation and understanding.

**Example:**

```java
@Bean
public Docket userApi() {
    return new Docket(DocumentationType.SWAGGER_2)
            .groupName("users")
            .select()
            .apis(RequestHandlerSelectors.basePackage("com.example.demo.controller"))
            .paths(PathSelectors.regex("/api/users.*"))
            .build();
}

@Bean
public Docket orderApi() {
    return new Docket(DocumentationType.SWAGGER_2)
            .groupName("orders")
            .select()
            .apis(RequestHandlerSelectors.basePackage("com.example.demo.controller"))
            .paths(PathSelectors.regex("/api/orders.*"))
            .build();
}
```

## 🔑 Best Practices

- **Keep Your Documentation Updated**: Ensure that the Swagger documentation is always in sync with your API code.
- **Use Meaningful Descriptions**: Provide detailed descriptions for APIs, parameters, and models to enhance clarity.
- **Leverage Swagger UI for Testing**: Use the interactive Swagger UI to test your API endpoints during development.

## 📚 Further Reading

- [Official Swagger Documentation](https://swagger.io/docs/)
- [Springfox GitHub Repository](https://github.com/springfox/springfox)
- [OpenAPI Specification](https://swagger.io/specification/)

---
