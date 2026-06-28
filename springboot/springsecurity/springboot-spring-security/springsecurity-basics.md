🔵🟢🔴➡️⭕🟠🟦🟣🟥🟧✔️
☑️ • ‣ → ⁕ ⏺️

# ⏺️ Spring Security

- Provides comprehensive security services, including `authentication`, `authorization`, and protection against common vulnerabilities like `CSRF`, `XSS`, `session fixation`, and more.

## ➡️ Spring Security Dependecny

```java

    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-security</artifactId>
    </dependency>
```

## ➡️ JWT Dependencies

```java
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt-api</artifactId>
    <version>0.11.5</version>
</dependency>
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt-impl</artifactId>
    <scope>runtime</scope>
    <version>0.11.5</version>
</dependency>
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt-jackson</artifactId>
    <scope>runtime</scope>
    <version>0.11.5</version>
</dependency>


```

## ➡️ Features

#### 🟦 Authentication:

#### 🟦 Authorization:

#### 🟦 Protection Against Common Attacks:

#### 🟦 CSRF and CORS Support:

#### 🟦 Integration:

#### 🟦 Password Encoding:

#### 🟦 Extensibility:

#### 🟦 Spring Security OAuth:

## Flow Diagram

# Spring Security Request Flow

# Concepts

# Security Concepts Overview

| Concept                   | Description                                                                       | Common Use Cases                                                         |
| ------------------------- | --------------------------------------------------------------------------------- | ------------------------------------------------------------------------ |
| **Authentication**        | Verifies user identity using credentials (e.g., username/password, JWT, OAuth2).  | Login forms, API tokens, database user authentication                    |
| **Authorization**         | Controls user access to specific actions or resources based on roles/permissions. | Role-based access (e.g., `hasRole()`, `@PreAuthorize`)                   |
| **Security Filter Chain** | Applies ordered filters to secure all incoming requests.                          | Global security across all application endpoints                         |
| **CSRF Protection**       | Blocks unauthorized POST/PUT requests from external sites.                        | Web apps with forms                                                      |
| **PasswordEncoder**       | Hashes passwords securely for safe storage.                                       | User registration and login                                              |
| **UserDetailsService**    | Fetches user data (e.g., roles, passwords) from a data source.                    | Database or in-memory user management                                    |
| **Session Management**    | Handles user sessions and prevents session fixation attacks.                      | Stateful web applications                                                |
| **JWT/OAuth2**            | Uses tokens for stateless authentication in secure API access.                    | REST APIs, Single Page Applications (SPAs)                               |
| **@PreAuhtorize**         | supports complex expressions (SpEL) for advanced authorization logic.             | `Flexible Rule`, `                                                       |
| **@Secured**              | Simpler syntax for basic role checks                                              | Must enable with `@EnableGlobalMethodSecurity(securedEnabled = true)`, ` |
