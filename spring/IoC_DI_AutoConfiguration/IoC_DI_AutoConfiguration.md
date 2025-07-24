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
