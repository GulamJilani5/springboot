⏺️ ➡️ 🟦 🔵 🟢🔴⭕🟠🟣🟥🟧✔️ ☑️ • ‣ → ⁕

# ⏺️ 2. @SpringBootApplication

- It's been run after **ApplicationContext** is created using `Application.run(....)`.

- **pom.xml (Maven)**

```java
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>
```

## ➡️ 2.1 @SpringBootConfiguration(@Configuration)

- A specialized form of **@Configuration**.
- Marks the main class as the primary source of bean definitions
- Tells Spring: This class contains (or imports) configuration for the entire application.
- Allows defining beans using **@Bean**

- Why Boot uses it (instead of plain **@Configuration**)
  - Helps Spring Boot identify the main configuration class
  - Used internally during startup for:
    - Auto-configuration ordering
    - Application context bootstrapping

## ➡️ 2.2 @ComponentScan

- Automatically scans packages to find Spring-managed components
- Scans current package + all sub-packages
- All classes annotated with: **@Component**, **@Service**, **@Repository**, **@Controller**, **@RestController** are detected, creates their objects, manages their(objects) entire lifecycle, and stores them in the Spring IoC container. These beans can then be injected into other classes using dependency injection, commonly via **@Autowired** or constructor injection.

- **What happens internally**
  - Spring reads the package of the main class
  - Recursively scans sub-packages
  - Finds stereotype annotations
  - Creates bean definitions
  - Stores them in the IoC container and manage the objects lifecycle and inject using **@Autowired** or constructor injection.

## ➡️ 2.3 @EnableAutoConfiguration

- Automatically configures Spring beans based on classpath, properties, and environment.

- **How Starters Enable Auto-Configuration**
  - Starter adds libraries to classpath
  - Boot detects libraries
  - Conditional auto-configurations activate
  - Beans are created only if missing
  - You can override with custom **@Bean**

#### 🟦 `spring-boot-starter-web` = Spring MVC + Embedded Tomcat + Jackson + Validation + Auto-configuration

- **What Boot auto-creates:**
  - DispatcherServlet
  - RequestMappingHandlerMapping
  - HttpMessageConverters
  - Embedded servlet container (Tomcat by default)

#### 🟦 `spring-boot-starter-data-jpa` = JPA + Hibernate + Spring Data + Spring ORM + Spring TX (transaction management) + Auto-configuration

- **What Spring Boot auto-configures**
  - DataSource
  - From spring.datasource.
  - EntityManagerFactory
  - Hibernate SessionFactory
  - JpaTransactionManager
  - JpaRepository implementations
  - DDL execution (ddl-auto=update/create)

#### 🟦 `spring-boot-starter-security` = Spring Security Core + Spring Security Web + Spring Security Config + Authentication + Authorization + Filter Chain + Auto-configuration

- **What Spring Boot auto-configures**
  - SecurityFilterChain
  - Default authentication
  - In-memory user
  - Generated password at startup
  - CSRF protection
  - Session management
  - Basic authentication
  - Form login

#### 🟦 @ConditionalOnClass

```java
@ConditionalOnClass(DataSource.class)

```

- DataSource library present → OK
- Not present → skip configuration

#### 🟦 @ConditionalOnMissingBean

```java
 @ConditionalOnMissingBean(DataSource.class)

```

- You did NOT define DataSource → auto-create one
- You defined one → Spring Boot backs off
- This is how Spring Boot never overrides your code

#### 🟦 @ConditionalOnProperty

```java
@ConditionalOnProperty(
  name = "spring.jpa.show-sql",
  havingValue = "true"
)

```

- Property enabled → apply config
- Disabled → skip

#### 🟦 @ConditionalOnWebApplication

- Web app → apply
- Non-web → skip
