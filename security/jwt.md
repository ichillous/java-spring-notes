---

# 🔐 JWT Authentication with Spring Security

JSON Web Tokens (JWT) are a popular method for securing RESTful APIs. JWTs are compact, URL-safe tokens that can be used to securely transmit information between parties as a JSON object. In the context of Spring Security, JWTs are commonly used for stateless authentication, where a token is issued after a user authenticates and is used for subsequent requests.

## 🎯 Key Concepts of JWT

- **Token-Based Authentication**: Unlike traditional session-based authentication, JWT is stateless. The server does not store any session data; instead, the JWT itself contains all the necessary information.
- **Claims**: JWTs can carry claims, which are pieces of information asserted about the token subject (like user ID, roles, etc.).
- **Signature**: JWTs are signed to ensure that the claims cannot be altered. This signature is verified by the server to confirm the token's integrity.

## 🛠️ Setting Up JWT in Spring Security

### 1. **Add Dependencies**

Ensure your `pom.xml` includes the necessary dependencies for JWT and Spring Security.

```xml
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt</artifactId>
    <version>0.9.1</version>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

### 2. **Creating a JWT Utility Class**

A utility class is needed to generate and validate JWT tokens.

**Example:**

```java
package com.example.demo.security;

import io.jsonwebtoken.Claims;
import io.jsonwebtoken.Jwts;
import io.jsonwebtoken.SignatureAlgorithm;
import org.springframework.stereotype.Component;

import java.util.Date;
import java.util.function.Function;

@Component
public class JwtUtil {

    private String SECRET_KEY = "secret";

    public String extractUsername(String token) {
        return extractClaim(token, Claims::getSubject);
    }

    public Date extractExpiration(String token) {
        return extractClaim(token, Claims::getExpiration);
    }

    public <T> T extractClaim(String token, Function<Claims, T> claimsResolver) {
        final Claims claims = extractAllClaims(token);
        return claimsResolver.apply(claims);
    }
    
    private Claims extractAllClaims(String token) {
        return Jwts.parser().setSigningKey(SECRET_KEY).parseClaimsJws(token).getBody();
    }

    private Boolean isTokenExpired(String token) {
        return extractExpiration(token).before(new Date());
    }

    public String generateToken(String username) {
        return createToken(username);
    }

    private String createToken(String subject) {
        return Jwts.builder()
                .setSubject(subject)
                .setIssuedAt(new Date(System.currentTimeMillis()))
                .setExpiration(new Date(System.currentTimeMillis() + 1000 * 60 * 60 * 10)) // 10 hours
                .signWith(SignatureAlgorithm.HS256, SECRET_KEY)
                .compact();
    }

    public Boolean validateToken(String token, String username) {
        final String extractedUsername = extractUsername(token);
        return (extractedUsername.equals(username) && !isTokenExpired(token));
    }
}
```

### 3. **Creating a JWT Authentication Filter**

This filter intercepts requests, extracts the JWT token, and validates it. If the token is valid, the user is authenticated.

**Example:**

```java
package com.example.demo.security;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.security.core.context.SecurityContextHolder;
import org.springframework.security.core.userdetails.UserDetailsService;
import org.springframework.security.web.authentication.WebAuthenticationDetailsSource;
import org.springframework.stereotype.Component;
import org.springframework.web.filter.OncePerRequestFilter;

import javax.servlet.FilterChain;
import javax.servlet.ServletException;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

@Component
public class JwtAuthenticationFilter extends OncePerRequestFilter {

    @Autowired
    private JwtUtil jwtUtil;

    @Autowired
    private UserDetailsService userDetailsService;

    @Override
    protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response, FilterChain chain)
            throws ServletException, IOException {

        final String authorizationHeader = request.getHeader("Authorization");

        String username = null;
        String jwt = null;

        if (authorizationHeader != null && authorizationHeader.startsWith("Bearer ")) {
            jwt = authorizationHeader.substring(7);
            username = jwtUtil.extractUsername(jwt);
        }

        if (username != null && SecurityContextHolder.getContext().getAuthentication() == null) {

            var userDetails = userDetailsService.loadUserByUsername(username);

            if (jwtUtil.validateToken(jwt, userDetails.getUsername())) {

                var authenticationToken = new UsernamePasswordAuthenticationToken(
                        userDetails, null, userDetails.getAuthorities());
                authenticationToken.setDetails(new WebAuthenticationDetailsSource().buildDetails(request));
                SecurityContextHolder.getContext().setAuthentication(authenticationToken);
            }
        }
        chain.doFilter(request, response);
    }
}
```

### 4. **Configuring Security**

Configure Spring Security to use the JWT filter and define which endpoints require authentication.

**Example:**

```java
package com.example.demo.config;

import com.example.demo.security.JwtAuthenticationFilter;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
import org.springframework.security.config.http.SessionCreationPolicy;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;
import org.springframework.security.crypto.password.PasswordEncoder;
import org.springframework.security.web.authentication.UsernamePasswordAuthenticationFilter;

@Configuration
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    @Autowired
    private JwtAuthenticationFilter jwtAuthenticationFilter;

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.csrf().disable()
            .authorizeRequests()
                .antMatchers("/api/auth/**").permitAll()
                .anyRequest().authenticated()
            .and()
            .sessionManagement().sessionCreationPolicy(SessionCreationPolicy.STATELESS);

        http.addFilterBefore(jwtAuthenticationFilter, UsernamePasswordAuthenticationFilter.class);
    }

    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }
}
```

### 5. **Creating the Authentication Controller**

This controller handles login requests, validates the credentials, and returns a JWT token if the credentials are correct.

**Example:**

```java
package com.example.demo.controller;

import com.example.demo.security.JwtUtil;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.security.authentication.AuthenticationManager;
import org.springframework.security.authentication.UsernamePasswordAuthenticationToken;
import org.springframework.security.core.AuthenticationException;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/api/auth")
public class AuthController {

    @Autowired
    private AuthenticationManager authenticationManager;

    @Autowired
    private JwtUtil jwtUtil;

    @PostMapping("/login")
    public String login(@RequestBody AuthRequest authRequest) throws AuthenticationException {
        authenticationManager.authenticate(
            new UsernamePasswordAuthenticationToken(authRequest.getUsername(), authRequest.getPassword()));
        return jwtUtil.generateToken(authRequest.getUsername());
    }
}
```

### 6. **Securing Endpoints**

Finally, secure your endpoints by requiring a valid JWT token to access them.

**Example:**

```java
@RestController
@RequestMapping("/api/secure")
public class SecureController {

    @GetMapping("/data")
    public String getSecureData() {
        return "This is a secure data";
    }
}
```

## 🔑 Best Practices

- **Keep Your Secret Key Safe**: Store the secret key securely and rotate it periodically.
- **Use Strong Encryption**: Always use strong encryption algorithms (e.g., HS256).
- **Set Short Token Expiry Times**: Reduce the risk of compromised tokens by setting short expiration times.
- **Validate JWTs Properly**: Ensure that you validate the token’s signature, claims, and expiration time on each request.
- **Use HTTPS**: Always secure your API with HTTPS to protect the JWT from being intercepted.

## 📚 Further Reading

- [JSON Web Tokens (JWT) Introduction](https://jwt.io/introduction/)
- [Spring Security Reference](https://docs.spring.io/spring-security/site/docs/current/reference/html5/)
- [Securing a RESTful API with Spring Security and JWT](https://www.baeldung.com/spring-security-oauth-jwt)

---
