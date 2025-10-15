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

# 🟦 Bean

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
