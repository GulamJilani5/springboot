🔵🟢🔴➡️⭕🟠🟦🟣🟥🟧✔️
☑️ • ‣ → ⁕ ⏺️

# ⏺️ Features of Spring Boot Starter Web for Handling HTTP Requests and Responses

## ➡️ 1. Handling HTTP Requests

### 🟦 Spring Web MVC Framework:

- @Controller or @RestController, @RequestMapping
- @GetMapping, @PostMapping, @PutMapping, @DeleteMapping

##### 🔵 Request Parameters

- `@RequestParam`, `@PathVariable`
- `@RequestBody:` Binds the request body to a Java object (**e.g.**, **JSON to POJO using Jackson**).

##### 🔵 Content Negotiation:

- Automatically handles different request content types (**e.g.**, `JSON`, `XML`) using **HttpMessageConverter** implementations (**e.g.**, `MappingJackson2HttpMessageConverter` for JSON).

##### 🔵 Validation:

- Integrates with **Bean Validation** using annotations like `@Valid` and `@NotNull` to validate incoming request bodies or parameters.

### 🟦 Request Headers:

- `@RequestHeader` or `HttpServletRequest`

### 🟦 🔴Form Data and File Uploads:

- Handles multipart/form-data requests for file uploads using @RequestPart or MultipartFile.

```java
@PostMapping("/upload")
public String uploadFile(@RequestPart("file") MultipartFile file) {
    return "Uploaded: " + file.getOriginalFilename();
}

```

### 🟦 Servlet API Integration:

- Provides access to low-level HttpServletRequest and HttpServletResponse objects for advanced use cases.

```java
@GetMapping("/session")
public String getSessionId(HttpServletRequest request) {
    return request.getSession().getId();
}
```

### 🟦 🔴 Filters and Interceptors:

- **Filters:** Use `javax.servlet.Filter` to intercept and modify requests before they reach controllers (**e.g.**, for logging, authentication).

- **Handler Interceptors:** Use **HandlerInterceptor** to add pre- and post-processing logic for requests.

```java
@Component
public class LoggingInterceptor implements HandlerInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) {
        log.info("Request URL: " + request.getRequestURI());
        return true;
    }
}
```

## 2. Handling HTTP Responses

### 🟦 Response Construction:

- Controllers return Java objects, which are automatically serialized to the response body (e.g., JSON, XML) using HttpMessageConverter.

```java
@GetMapping("/users/{id}")
public User getUser(@PathVariable Long id) {
    return userService.findById(id); // Automatically serialized to JSON
}
```

### 🟦 ResponseEntity:

- ResponseEntity for fine-grained control over HTTP status codes, headers, and body.

### 🟦 Status Codes:

- Spring MVC supports standard HTTP status codes (e.g., 200 OK, 404 Not Found, 500 Internal Server Error) via @ResponseStatus or ResponseEntity.

```java
@DeleteMapping("/users/{id}")
@ResponseStatus(HttpStatus.NO_CONTENT)
public void deleteUser(@PathVariable Long id) {
    userService.delete(id);
}
```

### 🟦 Response Headers:

- Set custom headers using ResponseEntity or HttpServletResponse.

```java
@GetMapping("/download")
public ResponseEntity<byte[]> downloadFile() {
    byte[] data = fileService.getFile();
    return ResponseEntity.ok()
        .header(HttpHeaders.CONTENT_DISPOSITION, "attachment; filename=\"file.pdf\"")
        .contentType(MediaType.APPLICATION_PDF)
        .body(data);
}
```

### 🟦 Content Negotiation:

- Automatically serves responses in the format requested by the client (e.g., JSON, XML) based on Accept headers or URL extensions (e.g., .json, .xml).

### 🟦 Exception Handling:

- @ExceptionHandler or @ControllerAdvice

### 🟦 Streaming Responses:

- Supports streaming responses for large data using ResponseBodyEmitter or StreamingResponseBody.

```java
@GetMapping("/stream")
public StreamingResponseBody streamData() {
    return outputStream -> {
        for (int i = 0; i < 100; i++) {
            outputStream.write(("Data: " + i + "\n").getBytes());
            Thread.sleep(100);
        }
    };
}

```

## 3. Additional Features for HTTP Handling

### 🟦 Embedded Servlet Container:

- spring-boot-starter-web includes an embedded servlet container (Tomcat by default) to handle HTTP requests without requiring an external server.
- Configurable via application.properties (e.g., server.port, server.servlet.context-path).

### 🟦 CORS Support:

- Enables **Cross-Origin Resource Sharing (CORS)** for handling requests from different domains, common in enterprise applications with frontend-backend separation.
- Configurable via `@CrossOrigin` or globally in `WebMvcConfigurer`.

```java
@CrossOrigin(origins = "http://frontend.example.com")
@RestController
public class UserController {
    @GetMapping("/users")
    public List<User> getUsers() {
        return userService.findAll();
    }
}

```

### 🟦 HATEOAS Support:

- Includes spring-boot-starter-hateoas (optional dependency) for building hypermedia-driven REST APIs, useful for enterprise APIs requiring discoverability.

```java
@GetMapping("/users/{id}")
public EntityModel<User> getUser(@PathVariable Long id) {
    User user = userService.findById(id);
    return EntityModel.of(user,
        linkTo(methodOn(UserController.class).getUser(id)).withSelfRel());
}
```

### 🟦 Static Content and MVC Views:

- Serves static resources (e.g., HTML, CSS, JS) from src/main/resources/static.
- Supports server-side view rendering with template engines like Thymeleaf (via spring-boot-starter-thymeleaf).

```java

@Controller
public class HomeController {
    @GetMapping("/")
    public String home(Model model) {
        model.addAttribute("message", "Welcome!");
        return "index"; // Renders index.html via Thymeleaf
    }
}

```

### 🟦 Observability:

- Integrates with Spring Actuator for monitoring HTTP endpoints (e.g., /actuator/health, /actuator/metrics).
- Supports Micrometer for metrics on HTTP requests/responses (e.g., request latency, status codes).

### 🟦 Security:

- Works with spring-boot-starter-security to secure HTTP endpoints with authentication and authorization (e.g., OAuth2, JWT).

## 4. Client-Side HTTP Handling

- `spring-boot-starter-web` is primarily for server-side HTTP **request/response** handling, it also includes tools for making HTTP requests to external services.

### 🟦 🔴RestTemplate (Legacy, still supported):

- A synchronous HTTP client for calling REST APIs.
- Configurable via RestTemplateBuilder for timeouts, headers, or interceptors.

### 🟦 🔴RestClient (Spring Boot 3.2+, Spring Framework 6.1+)

- A modern, fluent replacement for RestTemplate, included in `spring-boot-starter-web`.
- Supports JSON serialization, interceptors, and integration with the **Java 11** HTTP Client for HTTP/2.

```java
    @Autowired
    private RestClient restClient;

    public User getExternalUser(Long id) {
        return restClient.get()
            .uri("https://api.example.com/users/{id}", id)
            .retrieve()
            .body(User.class);
    }
```
