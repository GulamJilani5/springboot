# Inversion of Control (IoC):

- Design principle where the control of object creation, configuration, and lifecycle is inverted from the application code to an external framework or container.

- The **Spring Container** (specifically, the **Spring IoC Container**, often referred to as the **ApplicationContext** in Spring Boot) is Spring’s implementation of the **IoC principle**.

# Dependency Injection (DI):

- DI is a specific implementation of IoC

## How Spring Boot Implements IoC and DI

- Spring Boot uses the **Spring IoC Container**(container) to manage the lifecycle of beans and inject dependencies.
- IOC container is typically represented by the ApplicationContext in Spring Boot.
- When a Spring Boot application starts, the **ApplicationContext** is initialized, and it scans the codebase for beans (usually annotated with `@Component`, `@Service`, `@Repository`, etc.) and their dependencies.

### Dependency Injection in Spring Boot:

##### 1. Constructor Injection:(Recommended)

- Dependencies are provided through a class’s constructor.

##### 2. Setter Injection:

- Dependencies are injected via setter methods.

##### 3. Field Injection: (Mostly used)

- Dependencies are injected directly into fields (using @Autowired).

# Spring Boot Auto-Configuration

- Spring Boot’s auto-configuration is a powerful example of IoC

### Adding `spring-boot-starter-web` in your pom.xml

- Spring Boot automatically configures beans like **DispatcherServlet**, **WebMvcConfigurer**, and others.

### Adding `spring-boot-starter-data-jpa` in pom.xml

Spring Boot auto-configures:

- A **DataSource** bean for database connectivity.
- A **JpaRepository** implementation for your repositories.
- A **TransactionManager** for handling transactions.

We do not need to manually create these beans — Spring Boot’s **IoC Container** handles it based on the dependencies in your classpath.

# 5. Key Annotations for DI in Spring Boot

Here are the most commonly used annotations that enable DI and IoC in Spring Boot:

- `@Component:` Marks a class as a generic Spring-managed bean.
- `@Service:` Indicates a service-layer bean (specialized @Component).
- `@Repository:` Marks a data access bean, with additional exception translation for persistence.
- `@Controller / @RestController:` Marks a web controller bean.
- `@Autowired:` Requests Spring to inject a dependency (used for field, setter, or constructor injection).
- `@Qualifier:` Resolves ambiguity when multiple beans implement the same interface.
- `@Primary:` Specifies the preferred bean when multiple beans of the same type exist.
- `@Configuration:` Defines a class that provides bean definitions.
- `@Bean:` Declares a bean in a `@Configuration` class.

---

# Bean Factory

- Concrete implementation of the **Inversion of Control (IoC)** principle.
- **Managing Lifecycle:** The **BeanFactory** ensures beans are initialized and destroyed properly (e.g., calling `@PostConstruct` or `@PreDestroy` methods).

😶In Spring Boot, We rarely interact with the BeanFactory directly because the **ApplicationContext** (specifically, **AnnotationConfigApplicationContext** or similar implementations) is used by default. However, the **BeanFactory** is still the underlying foundation of the **ApplicationContext**.
