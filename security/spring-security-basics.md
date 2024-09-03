---

# 🔐 Spring Security Basics

Spring Security is a powerful and highly customizable authentication and access control framework for Java applications. It is the de-facto standard for securing Spring-based applications and provides comprehensive security services for Java EE-based enterprise software.

## 🎯 Why Spring Security?

- **Authentication and Authorization**: Easily manage user authentication and access control.
- **Extensibility**: Integrate with various authentication providers like LDAP, OAuth2, and JWT.
- **Protection Against Common Threats**: Automatically protects against CSRF, session fixation, clickjacking, and more.
- **Integration with Spring**: Seamlessly integrates with the Spring Framework, leveraging its DI, AOP, and MVC capabilities.

## 🛠️ Setting Up Spring Security

### 1. **Add Spring Security Dependency**

To get started with Spring Security, add the following dependency to your `pom.xml`:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

### 2. **Default Security Configuration**

By default, Spring Security secures all endpoints with basic authentication. When you add the Spring Security starter, your application is automatically secured.

**Default Behavior:**

- All HTTP endpoints are secured.
- A default login page is provided at `/login`.
- Default user credentials are generated and logged to the console (`user` as the username and a generated password).

## 🔑 Customizing Security Configuration

To customize the security configuration, you can extend `WebSecurityConfigurerAdapter` and override its methods.

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
                .antMatchers("/public/**").permitAll()
                .antMatchers("/admin/**").hasRole("ADMIN")
                .anyRequest().authenticated()
            .and()
            .formLogin()
                .loginPage("/login")
                .permitAll()
            .and()
            .logout()
                .permitAll();
    }

    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }
}
```

### 3. **Authentication and Authorization**

#### **Authentication**

Authentication is the process of verifying who a user is. Spring Security supports various authentication mechanisms:

- **In-Memory Authentication**: Useful for testing or simple applications.
- **JDBC Authentication**: Use a database to manage users and roles.
- **LDAP Authentication**: Integrate with an LDAP server for user management.
- **OAuth2 and JWT**: Modern authentication methods for stateless, token-based authentication.

**Example: In-Memory Authentication**

```java
@Override
protected void configure(AuthenticationManagerBuilder auth) throws Exception {
    auth.inMemoryAuthentication()
        .withUser("user").password(passwordEncoder().encode("password")).roles("USER")
        .and()
        .withUser("admin").password(passwordEncoder().encode("admin")).roles("ADMIN");
}
```

#### **Authorization**

Authorization determines what a user is allowed to do. It controls access to resources based on roles or authorities.

**Example: Role-Based Access Control**

```java
@Override
protected void configure(HttpSecurity http) throws Exception {
    http
        .authorizeRequests()
            .antMatchers("/admin/**").hasRole("ADMIN")
            .antMatchers("/user/**").hasRole("USER")
            .anyRequest().authenticated();
}
```

### 4. **Custom Login Page**

You can easily create a custom login page instead of using the default one provided by Spring Security.

**Example:**

```java
@Override
protected void configure(HttpSecurity http) throws Exception {
    http
        .formLogin()
            .loginPage("/login")
            .permitAll()
        .and()
        .logout()
            .permitAll();
}
```

Then create a simple login page in your `resources/templates` directory (e.g., `login.html` if using Thymeleaf):

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <title>Login</title>
</head>
<body>
    <h2>Login</h2>
    <form th:action="@{/login}" method="post">
        <div><label>Username: <input type="text" name="username"/></label></div>
        <div><label>Password: <input type="password" name="password"/></label></div>
        <div><input type="submit" value="Sign In"/></div>
    </form>
</body>
</html>
```

### 5. **Securing REST APIs**

To secure REST APIs, you typically use stateless authentication methods like JWT (JSON Web Tokens).

**Example: Stateless Security Configuration**

```java
@Override
protected void configure(HttpSecurity http) throws Exception {
    http
        .csrf().disable()
        .sessionManagement().sessionCreationPolicy(SessionCreationPolicy.STATELESS)
        .and()
        .authorizeRequests()
            .antMatchers("/api/auth/**").permitAll()
            .anyRequest().authenticated()
        .and()
        .addFilterBefore(new JwtAuthenticationFilter(), UsernamePasswordAuthenticationFilter.class);
}
```

## 📚 Further Reading

- [Spring Security Documentation](https://docs.spring.io/spring-security/site/docs/current/reference/html5/)
- [Spring Security Guides](https://spring.io/guides/gs/securing-web/)
- [Spring Security OAuth2](https://spring.io/guides/tutorials/spring-boot-oauth2/)

---

