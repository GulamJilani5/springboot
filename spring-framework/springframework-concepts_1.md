🔵🟢🔴➡️⭕🟠🟦🟣🟥🟧✔️⏺️ ☑️ • ‣ → ⁕

## ➡️ IOC

- Design principle where the control of object creation, configuration and lifecylce is inverted from the application code to an external framework(container).
- **Inversion of Control (IoC)** is a fundamental design principle in the **Spring Framework** that shifts the responsibility of managing object creation and dependencies from the application code to the framework itself.
- Spring's core is the **IoC container** (**e.g.**, `ApplicationContext`), which manages the lifecycle of objects (called `"beans"`). It reads configuration metadata (**XML**, **annotations**, or **Java config**) to instantiate, configure, and assemble beans.

## ➡️ Dependency Injection

- Specific implementation of **IOC.**

### 🟦 Constructor Injection:

- Dependencies are provided through a class constructor. This is the recommended approach as it ensures immutability and makes dependencies explicit.

```java
public class UserService {
    private final UserRepository repository;

    public UserService(UserRepository repository) {  // Constructor injection
        this.repository = repository;
    }
}
```

### 🟦 Setter Injection:

- Dependencies are set via setter methods. Useful for optional dependencies or when circular dependencies exist.

```java
 public class UserService {
    private UserRepository repository;

    public void setRepository(UserRepository repository) {  // Setter injection
        this.repository = repository;
    }
}
```

### 🟦 Field Injection:

- Dependencies are injected directly into fields (using annotations like `@Autowired`). Less recommended due to potential issues with immutability and testing.

```java
  public class UserService {
    @Autowired
    private UserRepository repository;  // Field injection
}
```

## ➡️ Bean Lifecycle and Scopes

## 🟦 Bean

- In Spring, a `"bean"` is an object managed by the `IoC container`. The bean lifecycle refers to the phases an object goes through from instantiation to destruction.
- A Bean in Spring is simply a Java object that is managed by the Spring IoC container.
- A bean is any Java object registered with the Spring container, which can then be injected into other beans as dependencies.
- When you declare a class as a bean, you’re telling Spring:
  `➔ “I want you to create, initialize, configure, and manage the lifecycle of this object.”`
  **🧠 Use Case:** Used to define services, repositories, or components that Spring should manage automatically. It ensures dependency injection, singleton scope, etc.
  **Annotations:** `@Component`, `@Service`, `@Repository`, `@Bean`.

## 🟦 Bean Lifecycle Phases:

#### 🔵 Instantiation:

- The container creates the bean instance using its constructor (or factory method).

#### 🔵 Population of Properties:

- Dependencies are injected (via constructor, setter, or field).
- Post-Processing (Aware **Interfaces**): If the bean implements interfaces like `BeanNameAware`, `BeanFactoryAware`, or `ApplicationContextAware`, Spring calls their methods to inject contextual information.

#### 🔵 Initialization:

- If the bean implements `InitializingBean`, `afterPropertiesSet()` is called.
- Custom init methods can be defined (e.g., via `@PostConstruct` or `XML` `init-method`).
- This is where you perform setup like opening connections or loading data.

#### 🔵 In Use:

- The bean is ready and can be used by the application.

#### 🔵 Destruction:

- If the bean implements `DisposableBean`, `destroy()` is called.
  Custom destroy methods via `@PreDestroy` or `XML` destroy-method.
  Useful for cleanup like closing resources.

## 🟦 Bean Scopes:

- Scopes define the lifecycle and visibility of a bean instance. The default is singleton.

#### 🔵 Singleton (Default)

- One instance per Spring IoC container. Shared across the application. Ideal for stateless services.
- - **Note:** When injecting a prototype bean into a singleton, use proxies (e.g., `@Scope(value = "prototype", proxyMode = ScopedProxyMode.TARGET_CLASS)`) to avoid sharing the same prototype instance.

```java
  @Bean
  @Scope("singleton")  // Explicit, but usually omitted
  public UserService userService() {
      return new UserService();
  }
```

#### 🔵 Prototype

- A new instance is created every time the bean is requested. Useful for stateful objects.

```java
  @Bean
  @Scope("prototype")
  public PrototypeBean prototypeBean() {
      return new PrototypeBean();
  }
```

#### 🔵 Request (Web-aware)

- One instance per HTTP request. Scoped to web requests in Spring MVC.

#### 🔵 Session (Web-aware)

- One instance per HTTP session.

#### 🔵 Application (Web-aware)

- One instance per ServletContext.

#### 🔵 WebSocket (Web-aware)

- One instance per WebSocket session.

#### 🔵 Custom Scope

- Custom scopes can be defined by implementing **Scope** interface.

## ➡️ Component Scanning, Bean Configuration and Property Binding

### 🟦 Component Scanning

- Component Scanning is the mechanism by which Spring automatically detects and registers beans in the application context based on classpath scanning.
- At its core, component scanning relies on the `<context:component-scan>` XML element (in XML-based configs) or the `@ComponentScan` annotation (in Java-based or annotation-driven configs).
- If no base package is specified, Spring defaults to scanning from the package of the configuration class (for @ComponentScan).

##### 🔵 Spring container starts up:

- **Classpath Scanning:** Spring scans the specified base packages (or sub-packages) for classes annotated with stereotype annotations.
- **Bean Detection:** It identifies classes marked as components (e.g., @Component, @Service, @Repository, @Controller).
- **Instantiation and Registration:** Detected classes are instantiated as singleton beans by default and registered in the ApplicationContext. Spring uses reflection to inspect annotations and apply post-processing (e.g., AOP proxies if @Transactional is present).
- **Dependency Resolution:** Dependencies (injected via @Autowired) are resolved during or after instantiation.

##### 🔵 Key Annotations for Component Scanning

- @Component, @Controller, @Service, @Repository, @Configuration
- These annotations can be combined with meta-annotations like `@Scope` (**e.g.**, `@Scope("prototype")`) to customize bean lifecycle.

### 🟦 Bean Configuration in Spring

- Bean configuration defines how objects are created, wired, and managed by the Spring IoC container.
- Spring supports multiple configuration styles: XML, Java-based (annotation-driven), and a hybrid approach.

#### Types of Bean Configuration

##### 🔵 XML-Based Configuration

- Uses `<beans>` root element in files like `applicationContext.xml`.
- Beans are defined via `<bean>` tags.

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="userService" class="com.example.service.UserService">
        <property name="userRepository" ref="userRepository"/>
    </bean>

    <bean id="userRepository" class="com.example.dao.UserRepository"/>
</beans>
```

- **Pros:** Human-readable, supports advanced features like <import>.
- **Cons:** Verbose, error-prone (typos in strings), hard to refactor.

##### 🔵 Annotation-Based Configuration:

- Leverages annotations like `@Component` (tied to scanning) for declaration.
- `@Bean` in `@Configuration` classes for method-based bean definitions.

```java
package com.example.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class AppConfig {

    @Bean
    public UserService userService() {
        UserService service = new UserService();
        service.setUserRepository(userRepository()); // Dependency injection
        return service;
    }

    @Bean
    public UserRepository userRepository() {
        return new UserRepository();
    }
}
```

- `@Configuration` classes are proxies for bean methods (via CGLIB) to handle singleton semantics.
- **Supports profiles:** `@Profile("dev")` for environment-specific beans.

##### 🔵 Java-Based Configuration (Pure):

- Replaces XML entirely using `@Configuration` and `@Bean`.
- **Advantages:** Compile-time safety, easier testing (e.g., `@ContextConfiguration` in **JUnit**).
- **Bean Scopes:** `@Scope("singleton")`, `@Scope("prototype")`, `@Scope("request")` (web).
- **Conditional Beans:** `@ConditionalOnMissingBean`, `@ConditionalOnClass`.

##### 🔵 Mixed Configuration:

- Use **@Import** to import XML or other `@Configuration` classes.
- Spring Boot favors convention-over-configuration, auto-wiring beans via starters.🔴
  - `D:\Jilani\learning\spring boot\springboot\springboot-1.md`

### 🟦 Property Binding in Spring

- Property binding (or externalized configuration) allows injecting configuration values (**e.g.**, `database URLs`, `API keys`) into beans without hardcoding. It promotes loose coupling and environment-specific setups.
- Spring supports binding from properties files, `YAML`, environment variables, or command-line args, with support for type conversion and validation since Spring 3.0.

#### Core Mechanisms

##### 🔵 @Value Annotation:

- Injects single values with SpEL (Spring Expression Language) support.
- **Sources:** `${property.key:default}` from `application.properties` or placeholders.

```java
  @Component
public class DatabaseConfig {
    @Value("${db.url:jdbc:h2:mem:testdb}") // Default if not set
    private String url;

    @Value("#{'${app.debug:true}' ? 'DEBUG' : 'INFO'}") // SpEL expression
    private String logLevel;

    public String getUrl() { return url; }
}
```

- Handles types: Strings, primitives, arrays (${list:1,2,3}), maps.
- **Validation:** Integrate with `@Validated` and Bean Validation

##### 🔵 @ConfigurationProperties (Spring Boot Enhancement):

- Binds to a prefix group of properties, mapping to a POJO for structured config.
- Requires `@EnableConfigurationProperties` or `@SpringBootApplication`.
- **Features:**

  - Relaxed binding: Supports kebab-case (app-name), camelCase, underscores.
  - Nested objects: `@ConfigurationProperties` on inner classes.
  - Validation: `@Validated` with `@NotNull`, `@Min(1)`.
  - YAML Support: In Spring Boot, `application.yml` for hierarchical configs.🔴

- **application.properties**

```java
app.name=MyApp
app.version=1.0
app.servers[0]=server1.example.com
app.servers[1]=server2.example.com
```

- **Config Class**

```java
  @ConfigurationProperties(prefix = "app")
@Component // Or @EnableConfigurationProperties(AppConfig.class)
public class AppConfig {
    private String name;
    private String version;
    private List<String> servers = new ArrayList<>();

    // Getters/Setters
    public String getName() { return name; }
    // ...
}
```

##### 🔵 Property Sources:

- Hierarchy `(Spring Boot order)`: `Command-line args` > `Env vars` > `application.properties` > `Defaults`.
- `@PropertySource("classpath:custom.properties")` to load extras.
- **Profiles:** `application-dev.properties` activated via `spring.profiles.active=dev`.

```java

```
