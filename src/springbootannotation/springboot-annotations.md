
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

## 2. @RestController
- **Purpose**: Combines `@Controller` and `@ResponseBody`.
- Used to define a controller where methods return JSON or XML directly.
- **Usage**:
  ```java
  @RestController
  public class MyController {
      @GetMapping("/hello")
      public String sayHello() {
          return "Hello!";
      }
  }
  ```

---

## 3. @RequestMapping
- **Purpose**: Maps HTTP requests to handler methods.
- Can be used at class or method level.
- **Usage**:
  ```java
  @RequestMapping("/api")
  public class ApiController {
      @RequestMapping("/hello")
      public String hello() {
          return "Hello API!";
      }
  }
  ```

---

## 4. @GetMapping / @PostMapping / @PutMapping / @DeleteMapping
- **Purpose**: Shortcut for `@RequestMapping(method = ...)`.
- Used to handle specific HTTP methods.
- **Usage**:
  ```java
  @GetMapping("/users")
  public List<User> getUsers() {
      // Logic here
  }
  ```

---

## 5. @Autowired
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

## 6. @Component / @Service / @Repository / @Controller
- **Purpose**: Marks a class as a Spring bean.
  - `@Component`: Generic stereotype.
  - `@Service`: For service layer.
  - `@Repository`: For DAO layer, with exception translation.
  - `@Controller`: For MVC controllers.

---

## 7. @PathVariable
- **Purpose**: Binds URL path parameters to method arguments.
- **Usage**:
  ```java
  @GetMapping("/users/{id}")
  public User getUser(@PathVariable Long id) {
      // Logic here
  }
  ```

---

## 8. @RequestParam
- **Purpose**: Extracts query parameters from the request.
- **Usage**:
  ```java
  @GetMapping("/search")
  public String search(@RequestParam String keyword) {
      // Logic here
  }
  ```

---

## 9. @RequestBody
- **Purpose**: Maps the body of the HTTP request to a Java object.
- **Usage**:
  ```java
  @PostMapping("/users")
  public User createUser(@RequestBody User user) {
      // Logic here
  }
  ```

---

## 10. @Entity
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

## 11. @Bean
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



## 12. @Value
- **Purpose**: Injects values from application properties (`application.properties` or `application.yml`) into fields.
- **Usage**:
  ```java
  @Value("${server.port}")
  private int serverPort;
  ```

---

## 13. @Configuration
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

## 14. @EnableAutoConfiguration
- **Purpose**: Tells Spring Boot to automatically configure beans based on the classpath and properties.
- **Note**: Already included in `@SpringBootApplication`, but knowing it helps during interviews.
- **Usage**:
  ```java
  @EnableAutoConfiguration
  public class MyApp { }
  ```

---

## 15. @EnableScheduling
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

## 16. @Transactional
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
