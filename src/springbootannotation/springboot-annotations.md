
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
