⏺️🔵🟦🟩🟢 ◼️🔹➡️ 🔷

# ⏺️ The spring-boot-starter-web

Dependency Includes:

- **spring-web** for Spring MVC.
- **jackson-databind** for JSON processing, which includes ObjectMapper.

### ➡️ spring-web

Core part of the Spring Framework that provides the infrastructure for building web applications, particularly those using the Model-View-Controller (MVC) pattern. It is designed to handle HTTP requests, process them, and return appropriate responses.

##### 🟢 RequestEntity

- **HTTP Method:** `GET`, `POST`, `PUT`, `DELETE`, etc.
- **URL:** The target endpoint.
- **Headers:** Request headers (**e.g.**, `Accept`, `Content-Type`).
- **Body:** The request payload (**e.g.**, a `Java object` or `raw data`).

```java
    @PostMapping("/Signup")
    public ResponseEntity<Map<String, String>> signup(@RequestEntity<Map<String, String>> requestEntity)
    // Access the request body
    /////Aproach 1 - Using @RequestEntity
      Map<String, String> signupInput = requestEntity.getBody();
    /////🟦Approach 2 - We can directly used `@RequestBody`
      @PostMapping("/signup")
      public ResponseEntity<Map<String, String>> signup( @RequestBody Map<String, String> reqBody)
      String username = reqBody.get("username");
      String password = reqBody.get("password");

    // Access headers
    ///// Aproach 1 - Using @RequestEntity
    String contentType = requestEntity.getHeaders().getContentType().toString();
    /////🟦Approach 2 - We can directly used `@RequestHeader`
    @PostMapping
    public ResponseEntity<Map<String, String>> signup(@RequestHeader Map<String, String> reqHeaders )
    String contetnType: reqHeaders.get("content-type");
    String host: reqHeaders.get("host");

    // Access method
    String method = requestEntity.getMethod().toString();

    // Access URL
    String url = requestEntity.getUrl().toString();

```

- 🔵`@RequestParam`(`?key=value`) and `@PathVariable`(`/user/generic`) Are Not Part of **RequestEntity**.
- `@RequestParam` and `@PathVariable` are part of Spring MVC’s annotation-based model, designed to simplify request handling by extracting specific components without needing the full RequestEntity. They are processed by Spring’s **HandlerMethodArgumentResolver** to bind request data to method parameters automatically.

- 🔷Using **RequestEntity** we can get the query paramater and path.

```java
    @GetMapping("/generic")
    public ResponseEntity<Map<String, String>> genericRequest(RequestEntity<Void> requestEntity)
        String query = requestEntity.getUrl().getQuery(); // e.g., "username=testuser"
        String path = requestEntity.getUrl().getPath();   // e.g., "/user/generic"java

```

- 🔷Using **@RequestParam**

```java

  // Method 1
  @PostMapping("/filter/{id}")
    public Map<String, String> filterMethod(@RequestParam Map<String, String> reqParam)
    String page = reqParam.get("page");
    String date = reqParam.get("date");

  // Method 2
   @PostMapping("/search")
   public ResponseEntity<Map<String, String>> searchUser(
        @RequestParam("page") String page,
        @RequestParam(value = "date", required = false) String date)

```

- 🔷Using **@PathVariable**

```java
@RestController
@RequestMapping("/api")
public class ProductController {

    @GetMapping("/products/{id}")
    public ResponseEntity<Map<String, String>> getProductById(@PathVariable("id") Long productId) {
        // Create a response body as a Map
        Map<String, String> response = new HashMap<>();
        response.put("Product ID", String.valueOf(productId));

        // Return with HTTP 200 OK
        return ResponseEntity.ok(response);
    }
}

```

##### 🟢 ResponseEntity

ResponseEntity allows to specify:

- **Body:** The response payload (**e.g.**, a `Java object`, `JSON`, or `plain text`).
- **Status Code:** The HTTP status (**e.g.**, `200` OK, `404` Not Found, `201` Created).
- **Headers:** Custom HTTP headers (**e.g.**, `Content-Type`, `Cache-Control`).

```java
return ResponseEntity.status(HttpStatus.CREATED)
                     .headers(headers)
                     .body(Map.of("Message", "Signup Successful"));
```

##### Spring MVC Framework

- **Model-View-Controller Architecture:** Spring MVC follows the MVC pattern, separating the application logic (Model), presentation layer (View), and request handling (Controller).
- **Controllers:** The `@Controller` and `@RestController` annotations allow developers to define classes that handle HTTP requests. Controllers map incoming requests to methods using annotations like `@RequestMapping`, `@GetMapping`, `@PostMapping`, `@PutMapping` `@DeleteMapping` etc.
- **Request Mapping:** Spring MVC provides flexible request mapping based on URL patterns, HTTP methods, headers, query parameters, and content types.

##### HTTP Message Conversion

- Spring Web provides **HttpMessageConverter** to convert HTTP request bodies to Java objects and Java objects to HTTP response bodies. For example, JSON data in a `POST` request can be automatically converted to a Java object using **MappingJackson2HttpMessageConverter** (powered by Jackson).

```java
@PostMapping("/users")
public User createUser(@RequestBody User user) {
    return userService.save(user);
}
```

`@RequestBody` annotation uses an **HttpMessageConverter** to deserialize the JSON payload into a User object.

##### RESTful Web Services:

- Spring MVC simplifies building **RESTful APIs** with annotations like `@RestController`, which combines `@Controller` and `@ResponseBody` to return data (**e.g.**, `JSON`) directly in the HTTP response.
- Supports **HTTP methods** (`GET`, `POST`, `PUT`, `DELETE`, etc.) and status codes for **RESTful** interactions.
- Provides features like content negotiation, where the server returns data in the format requested by the client (**e.g.**, `JSON or XML`) based on the Accept header.

##### Web Request Processing:

- 🟢 **DispatcherServlet:** The core of Spring MVC, DispatcherServlet, acts as the **front controller**, receiving all incoming HTTP requests and delegating them to appropriate **handlers** (`controllers`).
- **Interceptors:** **HandlerInterceptor** allows you to intercept requests before they reach the controller, after the controller processes them, or after the response is rendered. This is useful for **logging**, **authentication**, or modifying requests/responses.
- 🟢 **Exception Handling:** Spring MVC provides `@ExceptionHandler` and `@ControllerAdvice` to handle exceptions globally or within specific controllers, returning meaningful error responses (**e.g.**, HTTP `400` or `500`).

##### Support for Different View Technologies:

- Spring MVC supports multiple view technologies, such as Thymeleaf, JSP, FreeMarker, or JSON/XML for REST APIs.
  The **ViewResolver** maps logical view names (returned by controllers) to actual view implementations. For example, a **ModelAndView** object can specify a **Thymeleaf** template:

```java
@GetMapping("/home")
public ModelAndView home() {
    ModelAndView mav = new ModelAndView("home");
    mav.addObject("message", "Welcome to Spring MVC");
    return mav;
}
```

##### Form Handling and Validation:

- Spring MVC supports form processing with `@ModelAttribute` to bind form data to Java objects.
- Integrates with validation frameworks like **Hibernate Validator** using **@Valid** to enforce constraints (**e.g.**, `@NotNull`, `@Size`).

##### File Upload/Download:

- Supports multipart file uploads using **MultipartFile** in controllers.
- Facilitates file downloads by writing to the **HttpServletResponse** output stream.

##### 🟢 Asynchronous Request Processing:

- Supports asynchronous request handling with **Callable** or **DeferredResult** for long-running tasks, improving scalability.
- Integrates with reactive programming (via `spring-webflux`) for **non-blocking I/O**, although `spring-web` primarily uses **Servlet-based blocking I/O**.

```Java
@GetMapping("/async")
public DeferredResult<String> asyncRequest() {
    DeferredResult<String> result = new DeferredResult<>();
    new Thread(() -> {
        // Long-running task
        result.setResult("Done");
    }).start();
    return result;
}
```

##### Web Filters and Servlet Support:

- Provides filters like **HiddenHttpMethodFilter** to support RESTful operations (**e.g.**, converting `POST` to `PUT`/`DELETE`).
- Integrates with Servlet API for low-level control over HTTP requests and responses.

##### Integration with Other Spring Modules:

- Seamlessly integrates with Spring Security for **authentication/authorization**, **Spring Data** for database access, and Spring Boot for **auto-configuration**.
- Supports dependency injection in controllers using `@Autowired` or constructor injection.

##### Cross-Origin Resource Sharing (CORS):

- Provides @CrossOrigin annotation and global CORS configuration to allow cross-origin requests for APIs.

```java
@CrossOrigin(origins = "http://example.com")
@GetMapping("/data")
public Data getData() {
    return new Data();
}
```

### ➡️ jackson-databind

Core component of the Jackson project, used for serializing Java objects to JSON and deserializing JSON to Java objects.

##### ObjectMapper:

- The **ObjectMapper** class is the primary entry point for JSON **serialization** and **deserialization**.
- **Serialization:** Converts Java objects to JSON strings or streams.

##### Automatic JSON Binding:

In Spring MVC, `jackson-databind` is used by **MappingJackson2HttpMessageConverter** to automatically convert:

- HTTP request bodies (**JSON**) to Java objects via `@RequestBody`.
- Java objects to HTTP response bodies (**JSON**) via `@ResponseBody` or `@RestController`.

##### Customization

**Annotations:** Jackson provides annotations to customize serialization/deserialization:

- **@JsonProperty:** Maps JSON fields to Java fields.
- **@JsonIgnore:** Excludes fields from serialization/deserialization.
- **@JsonInclude:** Controls inclusion of null or empty fields.
- **@JsonFormat:** Formats dates or other types.

##### Support for Complex Data Structures:

- Handles nested objects, lists, maps, and arrays.
- Supports polymorphic types using @JsonTypeInfo and @JsonSubTypes for deserializing JSON into the correct subclass.

##### Streaming API:

- Provides low-level streaming APIs (JsonParser and JsonGenerator) for processing large JSON data efficiently.
- Useful for handling large datasets without loading the entire JSON into memory.

##### Tree Model:

Allows JSON to be represented as a tree of JsonNode objects, enabling flexible manipulation of JSON data.

##### Error Handling:

- Provides detailed error messages for JSON parsing issues, such as missing fields or type mismatches.
- Supports custom error handling via DeserializationProblemHandler.

##### Configuration Options:

ObjectMapper can be configured to:

- Ignore unknown properties (DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES).
- Include or exclude null values (SerializationFeature.WRITE_NULL_MAP_VALUES).

##### Integration with Spring:

- Spring Boot `auto-configures` **jackson-databind** as the default JSON processor for REST APIs.
- Developers can customize the **ObjectMapper** bean to tweak `serialization/deserialization` behavior globally:

##### Support for Java 8 and Beyond:

- With additional modules like `jackson-datatype-jsr310`, it supports **Java 8** `LocalDate`, `LocalDateTime`, and other modern Java types.
