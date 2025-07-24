🔴🔵☑️✔️ ➡️

# Spring Boot Annotations

## 1. @SpringBootApplication
- **Purpose**: Main entry point of any Spring Boot application.
- **Combines**: `@Configuration`, `@EnableAutoConfiguration`, and `@ComponentScan`.
- **Usage**:
  ```java
  @SpringBootApplication
  public class MyApp {
      public static void main(String[] args) {
          SpringApplication.run(MyApp.class, args);
      }
  }
  ```

---

## 2. @Autowired
- **Purpose**: Injects dependencies automatically (like services or repositories).
- **Usage**:
  ```java
  @Service
  public class MyService {
      // Logic here
  }

  @RestController
  public class MyController {
      @Autowired
      private MyService myService;
  }
  ```

---

## 3. @Component / @Service / @Repository / @Controller
- **Purpose**: Marks a class as a Spring bean.
  - `@Component`: Generic stereotype.
  - `@Service`: For service layer.
  - `@Repository`: For DAO layer, with exception translation.
  - `@Controller`: For MVC controllers.

---



## 4. @Entity
- **Purpose**: Marks a class as a JPA entity (used for database mapping).
- **Usage**:
  ```java
  @Entity
  public class User {
      @Id
      @GeneratedValue
      private Long id;
      private String name;
  }
  ```

---

## 5. @Bean
- **Purpose** : Declares a bean manually in a @Configuration class. Spring will manage the returned object
      as a Spring bean. Used when you want more control over the bean creation (e.g., setting custom 
      properties or using third-party classes not annotated with @Component, @Service, etc.).

- **Usage**:
  ```Java
  @Configuration
    public class AppConfig {
  
        @Bean
        public RestTemplate restTemplate() {
            return new RestTemplate();
        }
    }
  ```
- **Interview Tip** : Be ready to explain the difference between @Bean and @Component.
 - @Component is auto-detected via component scanning.
 - @Bean is declared manually in a Java config class (@Configuration).



## 6. @Value
- **Purpose**: Injects values from application properties (`application.properties` or `application.yml`) into fields.
- **Usage**:
  ```java
  @Value("${server.port}")
  private int serverPort;
  ```

---

## 7. @Configuration
- **Purpose**: Marks a class as a source of Spring bean definitions.
- Often used for defining beans manually using `@Bean`.
- **Usage**:
  ```java
  @Configuration
  public class AppConfig {
      @Bean
      public MyService myService() {
          return new MyService();
      }
  }
  ```

---

## 8. @EnableAutoConfiguration
- **Purpose**: Tells Spring Boot to automatically configure beans based on the classpath and properties.
- **Note**: Already included in `@SpringBootApplication`, but knowing it helps during interviews.
- **Usage**:
  ```java
  @EnableAutoConfiguration
  public class MyApp { }
  ```

---

## 9. @EnableScheduling
- **Purpose**: Enables Spring's scheduled task execution capability.
- **Usage**:
  ```java
  @Configuration
  @EnableScheduling
  public class SchedulerConfig { }

  @Component
  public class MyScheduler {
      @Scheduled(fixedRate = 5000)
      public void runEveryFiveSeconds() {
          System.out.println("Running scheduled task...");
      }
  }
  ```

---

## 10. @Transactional
- **Purpose**: Manages transactions declaratively. It ensures a method runs inside a transaction.
- Prevents partial data commits if something fails.
- **Usage**:
  ```java
  @Service
  public class MyService {
      @Transactional
      public void performDatabaseOperation() {
          // DB logic here
      }
  }
  ```

---


## 11. @Qualifier
- When you have multiple beans of the same type, Spring doesn't know which one to inject using **@Autowired**.
- **@Qualifier** specifies which bean to inject by name.


