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
- Enables exception translation (e.g., `SQLException` to `DataAccessException`).

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

### ➡️ @Configuration, @Bean, @Autowired, @Qualifier & @Primary

##### 🟦 @Configuration

- It tells Spring, "This class contains configuration logic, and you should look here for bean definitions (objects Spring manages)".
- Marks a class as a source of bean definitions. Replaces **XML** config.

##### 🟦 @Bean

- It tells Spring, "The object returned by this method should be registered as a bean in the application context." This makes the object available for dependency injection throughout your application.
- Supports lifecycle methods (**e.g.**, `initMethod`, `destroyMethod`).
- Methods annotated with `@Bean` return an object (e.g., an instance of a class). And this object will be managed by the spring framework. And this object will be used across the application (**e.g.**, `controllers`, `services`, `repositories`) using:
  - **@Autowired:** For field, setter, or constructor injection.
  - **Constructor Injection:** Preferred for explicit dependencies and better testability.
  - **Manual Retrieval:** You can also get the bean from the `ApplicationContext` using context.getBean(MyService.class).

##### 🟦 Creating Custom Bean

- **Spring Boot** often reduces the need for explicit `@Configuration` and `@Bean` by auto-configuring common components (e.g., @Component, @Controller @Service, @Repository, DataSource for databases). However, we still need custom beans not covered by auto-configuration.
- In a **Spring Boot** project, create @Configuration classes with @Bean methods for custom beans in a dedicated configuration package, typically named config or configuration.
- In Spring Boot, main class annotated with **@SpringBootApplication**, and it will automatically pick up `@Configuration` classes and components.
- This ensures Spring Boot’s component scanning detects your `@Configuration` class. Keep it separate from **controllers**, **services**, or **repositories** for clarity and maintainability.

##### 🟦 @Autowired

- Enables automatic dependency injection.
- Can be on constructors, setters, or fields.
- By default, required; use `required = false` for optional.

##### 🟦 @Qualifier

- Resolves ambiguities when multiple beans of the same type exist. Specifies which bean to inject.

##### 🟦 @Primary

- Marks one bean as the default choice when multiple beans of the same type are available, avoiding ambiguity without needing @Qualifier

#### **Example**

- Assume you have an interface `MessageService` with two implementations, `EmailService` and `SMSService`. You want to define them as beans and control which one is injected.

- **Step 1:** Define the Interface and Implementations

```java

    public interface MessageService {
        String sendMessage();
    }

    public class EmailService implements MessageService {
        public String sendMessage() {
            return "Sending email...";
        }
    }

    public class SMSService implements MessageService {
        public String sendMessage() {
            return "Sending SMS...";
        }
    }
```

- **Step 2:** Create a @Configuration Class with @Bean, @Primary, and @Qualifier

```java
    import org.springframework.context.annotation.Configuration;
    import org.springframework.context.annotation.Bean;
    import org.springframework.context.annotation.Primary;

    @Configuration
    public class AppConfig {

        @Bean
        @Primary // This bean is the default when no qualifier is specified
        public MessageService emailService() {
            return new EmailService();
        }

        @Bean
        @Qualifier("sms") // Name this bean "sms" for explicit injection
        public MessageService smsService() {
            return new SMSService();
        }
    }
```

- **Step 3:** Inject Beans Using @Autowired, @Qualifier, and @Primary

```java
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.beans.factory.annotation.Qualifier;
    import org.springframework.stereotype.Component;

    @Component
    public class MessageSender {

        private final MessageService defaultService; // Will get EmailService due to @Primary
        private final MessageService smsService;     // Will get SMSService due to @Qualifier

        @Autowired
        public MessageSender(MessageService defaultService, // No @Qualifier, so @Primary bean is used
                            @Qualifier("sms") MessageService smsService) { // Explicitly specify "sms" bean
            this.defaultService = defaultService;
            this.smsService = smsService;
        }

        public void sendMessages() {
            System.out.println("Default Service: " + defaultService.sendMessage()); // EmailService
            System.out.println("SMS Service: " + smsService.sendMessage());         // SMSService
        }
    }
```

- **@Primary:** EmailService is marked as `@Primary`, so it’s the default choice when Spring needs a MessageService bean and no `@Qualifier` is specified.
- **@Qualifier:** The sms qualifier explicitly selects the `SMSService` bean when injecting into the `smsService` field.

### ➡️ @Value

- Injects values from properties files, environment variables, or `SpEL` (**Spring Expression Language**).
- Useful for externalizing configuration.

```java
    @Value("${app.name}")
    private String appName;  // From application.properties

    @Value("#{systemProperties['user.home']}")
    private String userHome;  // Using SpEL
```
