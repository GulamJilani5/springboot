🔵🟢🔴➡️⭕🟠🟦🟣🟥🟧✔️⏺️ ☑️ • ‣ → ⁕

# ⏺️ Stereotype Annotations

- Stereotype annotations are meta-annotations that mark classes as Spring-managed components, enabling component scanning.
- They are built on top of `@Component` and provide semantic meaning.
- These annotations trigger auto-detection during component scanning. They can include attributes like value for bean name (**e.g.**, `@Service("myService")`).

### ➡️ @Component

- It is a base annotation.
- It Indicates a class is a Spring bean. Used for generic components.

```java
    @Component
    public class GenericComponent {
        // ...
    }
```

### ➡️ @Service

- For service-layer classes (business logic). No functional difference from @Component, but improves readability.

```java
    @Service
    public class UserService {
        // ...
    }
```

### ➡️ @Repository

- For data access objects (DAOs).
- It Adds exception translation (**e.g.**, converts JDBC exceptions to `Spring's DataAccessException`).

```java
    @Repository
    public class UserRepository {
        // ...
    }
```

### ➡️ @Controller / @RestController

- For web controllers in Spring MVC. `@RestController` combines `@Controller` and `@ResponseBody`.

```java
   @RestController
    public class UserController {
        // ...
    }
```

### ➡️ Custom stereotypes can be created by annotating with @Component.

# ⏺️ @Configuration, @Bean, @Value, @Autowired, @Qualifier & @Primary

- These annotations are core to annotation-based configuration in Spring.

### ➡️ @Configuration

- Marks a class as a source of bean definitions. Replaces **XML** config. The class can contain `@Bean` methods.

```java
    @Configuration
    public class AppConfig {
        // Bean definitions here
   }
```

### ➡️ @Bean

- Defines a bean in a @Configuration class. The method name becomes the bean name (overridable with name attribute). Spring calls the method to create the bean.

```java
   @Configuration
    public class AppConfig {
        @Bean
        public UserService userService() {
            return new UserService();
        }
    }
```

- Supports lifecycle methods (e.g., `initMethod`, `destroyMethod`).

### ➡️ @Value

- Injects values from properties files, environment variables, or `SpEL` (**Spring Expression Language**).
- Useful for externalizing configuration.

```java
    @Value("${app.name}")
    private String appName;  // From application.properties

    @Value("#{systemProperties['user.home']}")
    private String userHome;  // Using SpEL
```

### ➡️ @Autowired

- Enables automatic dependency injection.
- Can be on constructors, setters, or fields.
- By default, required; use `required = false` for optional.

```java
   @Autowired
    public UserService(UserRepository repository) {  // Constructor autowiring
        // ...
    }
```

### ➡️ @Qualifier

- Resolves ambiguities when multiple beans of the same type exist. Specifies which bean to inject.

```java
  @Autowired
@Qualifier("primaryRepo")
private UserRepository repository;
```

### ➡️ @Primary

### ➡️

```java

```
