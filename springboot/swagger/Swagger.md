⏺️ ➡️ 🟦 🔵 🟢🔴⭕🟠🟣🟥🟧✔️☑️ • ‣ → ⁕

⏺️ Swagger

- Swagger is a toolset used to document, visualize, and test REST APIs.
- Swagger annotations are used in Controller only.🔴

- **Swagger**
  - Tooling (UI, Editor, Codegen)
  - `http://localhost:8080/swagger-ui.html`
- **OpenAPI**
  - Specification (API standard)
  - `http://localhost:8080/v3/api-docs`
- **springdoc-openapi**
  - Library that connects Spring Boot ↔ OpenAPI

### 🟦 Dependency

```java
<dependency>
  <groupId>org.springdoc</groupId>
  <artifactId>springdoc-openapi-starter-webmvc-ui</artifactId>
  <version>2.3.0</version>
</dependency>
```

### 🟦 Flow

```java
Controller → OpenAPI annotations
           → springdoc-openapi
           → OpenAPI JSON
           → Swagger UI
```

### 🟦 Enable / Disable Swagger

```java
springdoc:
  api-docs:
    enabled: true
  swagger-ui:
    enabled: true/false

```

## ➡️ Swagger Annotations

### 🟦 Class Level

```java
@Tag(name = "User API", description = "User management APIs")
@RestController
@RequestMapping("/users")

```

### 🟦 API Operation

```java
@Operation(
  summary = "Get user by ID",
  description = "Fetch user details using user ID"
)
@GetMapping("/{id}")
public User getUser(@PathVariable Long id) {
    return userService.getUser(id);
}

```

### 🟦 Parameters

```java
@Parameter(description = "User ID", example = "101")
@PathVariable Long id

```

### 🟦 Request Body

```java
@io.swagger.v3.oas.annotations.parameters.RequestBody(
  description = "User request payload",
  required = true
)

```

### 🟦 API Responses

```java
@ApiResponses({
  @ApiResponse(responseCode = "200", description = "Success"),
  @ApiResponse(responseCode = "404", description = "User not found")
})

```

### 🟦 Schema / Model Documentation

```java
@Schema(description = "User entity")
public class User {

  @Schema(example = "101")
  private Long id;

  @Schema(example = "john@example.com")
  private String email;
}

```

### 🟦 Swagger Security (JWT Example)

```java
@SecurityScheme(
  name = "bearerAuth",
  type = SecuritySchemeType.HTTP,
  scheme = "bearer",
  bearerFormat = "JWT"
)

```

```java
@SecurityRequirement(name = "bearerAuth")
@GetMapping("/secure")

```
