🔵🟢🔴➡️⭕🟠🟦🟣🟥🟧✔️⏺️ ☑️ • ‣ → ⁕

# ⏺️ Spring Boot Flow

```text
1. ApplicationContext created

2. @SpringBootApplication processed
   ├─ @Configuration → allows @Bean methods
   ├─ @ComponentScan → registers bean definitions (recipes)
   └─ @EnableAutoConfiguration → registers conditional bean definitions

3. ALL bean definitions collected

4. Bean lifecycle starts
   ├─ Instantiate beans
   ├─ Inject dependencies
   ├─ Lifecycle callbacks

5. Application ready

```

- `D:\Jilani\learning\spring boot\spring-framework\springframework-concepts_2.md`

## ➡️ Without Spring Boot (traditional Spring):

- Normally in Spring (without Spring Boot), you have to manually configure many things:
  - bean definitions, data sources, security settings
  - DataSource (database connection)
  - DispatcherServlet
  - Component scanning packages
  - ViewResolvers
  - Message converters, etc.
- This leads to verbose **XML** files, **Java config classes**, or **annotations scattered** everywhere.
- With Spring Boot, you follow some standard conventions, and Spring Boot automatically configures many things for you.

```java
@Configuration
@EnableWebMvc
@ComponentScan(basePackages = "com.example.demo")
public class AppConfig {
    // manually configure beans, view resolvers, data sources etc.
}

```

- You would also need a web.xml or DispatcherServlet configuration.

## ➡️ With Spring Boot (Spring Boot Flow)

- SpringConvention Over Configuration, @SpringBootApplication, spring Auto configuration and Spring bean management

- Just create a class with **@SpringBootApplication**.
- Put your code in the standard package structure.
- Add a few dependencies (starters), and run.

```java
@SpringBootApplication //  Spring Boot Configuration  + component scanning + Enables auto-configuration
public class MyApplication {
    public static void main(String[] args) {
        SpringApplication.run(MyApplication.class, args);
    }
}

```

- No manual XML.
- No explicit bean definitions for common stuff.
- Boot auto-configures things if you follow the conventions (e.g., putting your `@SpringBootApplication` at the root package and keeping components inside sub-packages).

### 🟦 1. Creating Context

- When `SpringApplication.run(App.class, args)` run,
- Spring says: “Okay, I will:
  - Create a container
  - Read your configuration
  - Find your classes
  - Create objects
  - Connect dependencies
  - Start the server
  - And make the app ready”
- Spring’s brain + memory
- Spring creates a central container (box) that will store and manage all your objects.
- When Spring creates the **ApplicationContext**, it:
  - Creates an empty container
  - Decides what type of app it is (web / non-web/ reactive)
  - Prepares to store beans

### 🟦 2. @SpringBootApplication

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

#### 🔵 2.1 @SpringBootConfiguration(@Configuration)

- A specialized form of **@Configuration**.
- Marks the main class as the primary source of bean definitions
- Tells Spring: This class contains (or imports) configuration for the entire application.
- Allows defining beans using **@Bean**

- Why Boot uses it (instead of plain **@Configuration**)
  - Helps Spring Boot identify the main configuration class
  - Used internally during startup for:
  - Auto-configuration ordering
  - Application context bootstrapping

#### 🔵 2.2 @ComponentScan

- Automatically scans packages to find Spring-managed components
- Scans current package + all sub-packages
- All classes annotated with: **@Component**, **@Service**, **@Repository**, **@Controller**, **@RestController** are detected, creates their objects, manages their(objects) entire lifecycle, and stores them in the Spring IoC container. These beans can then be injected into other classes using dependency injection, commonly via **@Autowired** or constructor injection.

- **What happens internally**
  - Spring reads the package of the main class
  - Recursively scans sub-packages
  - Finds stereotype annotations
  - Creates bean definitions
  - Stores them in the IoC container and manage the objects lifecycle and inject using **@Autowired** or constructor injection.

#### 🔵2.3 @EnableAutoConfiguration

- Automatically configures Spring beans based on classpath, properties, and environment.

- **How Starters Enable Auto-Configuration**
  - Starter adds libraries to classpath
  - Boot detects libraries
  - Conditional auto-configurations activate
  - Beans are created only if missing
  - You can override with custom **@Bean**

##### ⁕ `spring-boot-starter-web` = Spring MVC + Embedded Tomcat + Jackson + Validation + Auto-configuration

- **What Boot auto-creates:**
  - DispatcherServlet
  - RequestMappingHandlerMapping
  - HttpMessageConverters
  - Embedded servlet container (Tomcat by default)

##### ⁕ `spring-boot-starter-data-jpa` = JPA + Hibernate + Spring Data + Spring ORM + Spring TX (transaction management) + Auto-configuration

- **What Spring Boot auto-configures**
  - DataSource
  - From spring.datasource.
  - EntityManagerFactory
  - Hibernate SessionFactory
  - JpaTransactionManager
  - JpaRepository implementations
  - DDL execution (ddl-auto=update/create)

##### ⁕ `spring-boot-starter-security` = Spring Security Core + Spring Security Web + Spring Security Config + Authentication + Authorization + Filter Chain + Auto-configuration

- **What Spring Boot auto-configures**
  - SecurityFilterChain
  - Default authentication
  - In-memory user
  - Generated password at startup
  - CSRF protection
  - Session management
  - Basic authentication
  - Form login

### 🟦 Bean lifecycle starts

#### 🔵3.1 Instantiate beans

- Now Spring starts creating beans:
  - Singleton beans are created eagerly
  - Prototype beans are created on demand

#### 🔵3.2 Inject dependencies

- **Spring resolves:**
  - @Autowired
  - Constructor injection
  - Setter injection
  - @Value

- **Order:**
  - Create bean
  - Inject dependencies
  - Resolve circular dependencies (if possible)

#### 🔵3.3 Lifecycle callbacks

- Spring invokes:
  - Aware interfaces
  - @PostConstruct
  - InitializingBean
  - Custom init methods

### 🟦 4. Application ready
