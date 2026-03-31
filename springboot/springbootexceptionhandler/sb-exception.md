🔴🔵☑️✔️ ➡️ 🟩 🟢 🔷 ✓→•←⁕⁂※⁜‣

# 🟩 Global Exception Handling in Spring Boot

### 🟢 Annotations

- **@RestControllerAdvice** → Marks the class as a global REST exception handler.
- **@Order(Ordered.HIGHEST_PRECEDENCE)** → Ensures this handler takes highest priority.
- **@ExceptionHandler** → Defines methods to handle specific exceptions.

### 🟢 Standard Error Response

```java
public static class ErrorResponse {
    private String title;      // Short title (e.g., "Validation failed")
    private String message;    // Detailed error message
    private HttpStatus status; // HTTP status code
}

```

### 🟢 Logging

- `logger.warn()` → Validation, access, and user-level errors.
- `logger.info()` → Expected conditions (e.g., resource not found).
- `logger.error()` → Critical server errors & runtime exceptions.

### 🟢 Exception Types & Their Purpose

##### 🔷 Validation & Binding Errors

- `MethodArgumentNotValidException` → Invalid @Valid DTO fields.
- `ConstraintViolationException` → Invalid @Validated method params.
- `BindException → Binding errors` (form/query params).
- `HttpMessageNotReadableException` → Malformed JSON body.
- `MissingServletRequestParameterException` → Missing request parameter.
- `MethodArgumentTypeMismatchException` → Wrong type (e.g., String instead of Integer).

##### 🔷 Business & Domain Errors

- `IllegalArgumentException` → Invalid method arguments.
- `BusinessException` → Custom business logic failures.
- `ResourceNotFoundException` → Requested entity not found.
- `UserAlreadyExistsException` → Duplicate resource (e.g., user registration).

##### 🔷 Database Exception

- `DataIntegrityViolationException`
- `SQLException`

##### 🔷 Technical & Infrastructure Errors

- `DataAccessException` → Database errors.
- `MaxUploadSizeExceededException` → File upload size exceeded.
- `HttpMediaTypeNotSupportedException` → Unsupported content type.
- `HttpRequestMethodNotSupportedException` → Wrong HTTP method.
- `AsyncRequestTimeoutException` → Async request timeout.

##### 🔷 Security & Access Errors

- `AuthenticationException` → Authentication failures (401).
- `AccessDeniedException` → Forbidden access (403).

##### 🔷 Spring-specific Errors

- `ResponseStatusException` → Manually thrown status exceptions.
- `NoHandlerFoundException` → 404 for unmatched endpoints.

##### 🔷 Generic Errors

- `RuntimeException` → Catch-all for runtime issues.
- `Exception` → Final fallback for unhandled errors.

## OutOfMemoryError

- We do not required to handle such errors.
- Once it occurs, the JVM is in an unstable state — even if you catch it, the app might behave unpredictably.
- The correct approach is to prevent it (optimize code, fix memory leaks, tune JVM memory).
