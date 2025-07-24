# Mainly three ways to manage the configuration

### 1. @Value
-Injects individual property values directly into fields, constructor parameters, or method parameters.
```java
@Value("${server.port}")
private int serverPort;

```
### 2. Environment interface
- Programmatic way to access environment properties using the Environment object provided by Spring.
```java
@Autowired
private Environment env;

public void printPort() {
    String port = env.getProperty("server.port");
    System.out.println("Server port: " + port);
}
```
### 3. @ConfigurationProperties
- Binds a group of properties to a Java POJO for type-safe configuration.
- We can use `Record` class
- `@EnableConfigurationProperties` is used on the top of the **Main Class**.
- application.yml:
```java
app:
  name: MyApp
  timeout: 5000
```

- Java POJO
```java
  @Component
  @ConfigurationProperties(prefix = "app")
  public class AppConfig {
  private String name;
  private int timeout;

  // getters and setters
  }
```

# Profile
- `application_qa.yml`
- `application_prod.yml`
###  Externalizing Configurations
Externalizing configuration in Spring Boot means keeping application settings (like URLs, credentials, 
feature flags) outside the codebase, typically in application.yml, environment variables, or external 
config servers. This allows flexible configuration without modifying the code.
- **Command-Line**, **JVM** & **Environment options**

##### Advantage
- Promotes clean separation between code and environment-specific values.
- Supports dynamic configuration for different environments without code changes.
- Improves security (when secrets are managed externally) and simplifies CI/CD.

##### Disadvantage
- Poor management may lead to security risks or config drift.
- Spring Boot supports many config sources (files, env vars, cloud config, etc.). This can lead to confusion about which value is being used (due to overriding).
- Sensitive data in external files must be handled securely (e.g., not pushed to Git).

### Spring Cloud Config
Spring Cloud Config is a part of the Spring Cloud ecosystem that provides server-side and client-side 
support for externalized configuration in a distributed system. It allows you to centralize configuration 
for all your microservices in one location (e.g., a Git repository), making it easy to update, manage, 
and version the configuration across environments like dev, QA, and production.

| Use Case                                         | Feasibility                                 |
| ------------------------------------------------ | ------------------------------------------- |
| You have **multiple Spring Boot services**       | ✅ Highly feasible                           |
| You want to **manage all configs from Git**      | ✅ Very useful                               |
| You need **dynamic config refresh** (no restart) | ✅ Feasible with Actuator + Spring Cloud Bus |
| You want to **centralize secrets/configs**       | ✅ Especially with vault integrations        |
| You're using **Kubernetes / Cloud**              | ✅ Works well with cloud-native patterns     |


**✅ 3 Different Ways to Use Spring Cloud Config**
##### 1. Spring Cloud Config Server with Git Backend (most common)
##### 2. Spring Cloud Config Server with File System Backend
##### 3. Spring Cloud Config with Vault Backend (for secure secrets management)















