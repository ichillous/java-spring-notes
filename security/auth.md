---

# 🔑 Authentication in Spring Security

Authentication is the process of verifying the identity of a user or entity. In Spring Security, authentication is a crucial aspect, ensuring that only authorized users can access specific parts of the application.

## 🎯 Key Concepts in Authentication

- **Principal**: The user or entity that has been authenticated.
- **Credentials**: The data (such as a password) used to authenticate the principal.
- **Authentication**: The process of verifying the credentials provided by the principal.
- **AuthenticationManager**: The core interface in Spring Security responsible for processing authentication requests.

## 🛠️ Setting Up Authentication

### 1. **In-Memory Authentication**

In-memory authentication is useful for testing or simple applications where user data does not need to be persisted.

**Example:**

```java
package com.example.demo.config;

import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.authentication.builders.AuthenticationManagerBuilder;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;
import org.springframework.security.crypto.password.PasswordEncoder;

@Configuration
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        auth.inMemoryAuthentication()
            .withUser("user")
            .password(passwordEncoder().encode("password"))
            .roles("USER")
            .and()
            .withUser("admin")
            .password(passwordEncoder().encode("admin"))
            .roles("ADMIN");
    }

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
            .authorizeRequests()
                .antMatchers("/admin/**").hasRole("ADMIN")
                .antMatchers("/user/**").hasRole("USER")
                .anyRequest().authenticated()
            .and()
            .formLogin()
                .loginPage("/login")
                .permitAll()
            .and()
            .logout()
                .permitAll();
    }

    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }
}
```

### 2. **JDBC Authentication**

JDBC authentication allows you to authenticate users against a database. Spring Security provides default queries to retrieve user details and roles from the database.

**Example:**

```java
package com.example.demo.config;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.authentication.builders.AuthenticationManagerBuilder;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;
import org.springframework.security.crypto.password.PasswordEncoder;

import javax.sql.DataSource;

@Configuration
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    @Autowired
    private DataSource dataSource;

    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        auth.jdbcAuthentication()
            .dataSource(dataSource)
            .passwordEncoder(passwordEncoder());
    }

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
            .authorizeRequests()
                .antMatchers("/admin/**").hasRole("ADMIN")
                .antMatchers("/user/**").hasRole("USER")
                .anyRequest().authenticated()
            .and()
            .formLogin()
                .loginPage("/login")
                .permitAll()
            .and()
            .logout()
                .permitAll();
    }

    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }
}
```

### 3. **Custom User Details Service**

For more complex applications, you may need to load user-specific data from a custom source. This can be achieved by implementing the `UserDetailsService` interface.

**Example:**

```java
package com.example.demo.service;

import com.example.demo.model.User;
import com.example.demo.repository.UserRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.security.core.userdetails.UserDetails;
import org.springframework.security.core.userdetails.UserDetailsService;
import org.springframework.security.core.userdetails.UsernameNotFoundException;
import org.springframework.stereotype.Service;

@Service
public class CustomUserDetailsService implements UserDetailsService {

    @Autowired
    private UserRepository userRepository;

    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
        User user = userRepository.findByUsername(username);
        if (user == null) {
            throw new UsernameNotFoundException("User not found");
        }
        return new org.springframework.security.core.userdetails.User(user.getUsername(), user.getPassword(), user.getAuthorities());
    }
}
```

### 4. **Authentication Providers**

Spring Security provides a flexible way to define custom authentication mechanisms using `AuthenticationProvider`. You can implement your own authentication logic or use existing providers.

**Example: Custom Authentication Provider**

```java
package com.example.demo.security;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.security.authentication.AuthenticationProvider;
import org.springframework.security.authentication.UsernamePasswordAuthenticationToken;
import org.springframework.security.core.Authentication;
import org.springframework.security.core.AuthenticationException;
import org.springframework.security.core.userdetails.UserDetailsService;
import org.springframework.stereotype.Component;

@Component
public class CustomAuthenticationProvider implements AuthenticationProvider {

    @Autowired
    private UserDetailsService userDetailsService;

    @Override
    public Authentication authenticate(Authentication authentication) throws AuthenticationException {
        String username = authentication.getName();
        String password = authentication.getCredentials().toString();

        // Implement your custom authentication logic here

        return new UsernamePasswordAuthenticationToken(username, password, new ArrayList<>());
    }

    @Override
    public boolean supports(Class<?> authentication) {
        return authentication.equals(UsernamePasswordAuthenticationToken.class);
    }
}
```

### 5. **OAuth2 and JWT**

For stateless authentication, Spring Security offers OAuth2 and JWT support, which is ideal for RESTful APIs. These tokens are generated after successful authentication and used for subsequent requests.

**Example: Configuring JWT**

```java
package com.example.demo.config;

import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;

@EnableWebSecurity
public class JwtSecurityConfig extends WebSecurityConfigurerAdapter {

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
            .csrf().disable()
            .authorizeRequests()
                .antMatchers("/api/auth/**").permitAll()
                .anyRequest().authenticated()
            .and()
            .addFilter(new JwtAuthenticationFilter(authenticationManager()));
    }
}
```

## 🔑 Best Practices

- **Use Strong Password Encoding**: Always encode passwords with strong algorithms like BCrypt.
- **Implement Account Locking and Password Policies**: Protect against brute force attacks.
- **Use HTTPS**: Ensure all authentication requests are secured with HTTPS.
- **Limit Authentication Attempts**: Implement a mechanism to limit the number of failed login attempts.
- **Regularly Update Security Dependencies**: Keep your security libraries up to date to protect against known vulnerabilities.

## 📚 Further Reading

- [Spring Security Reference](https://docs.spring.io/spring-security/site/docs/current/reference/html5/)
- [Spring Security OAuth2 Guide](https://spring.io/guides/tutorials/spring-boot-oauth2/)
- [JWT in Spring Security](https://www.baeldung.com/spring-security-oauth-jwt)

---
