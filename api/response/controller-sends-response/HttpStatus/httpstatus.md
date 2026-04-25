⏺️ ➡️ 🟦 🟩 🟢 🔵 🔷 🔹🔴 ☑️ ✔️ ✓→•←⁕⁂※⁜‣

# ⏺️ HTTP Status vs HttpStatusCode vs using ResponseEntity

### ➡️ HttpStatus

- **Java Enum** that contains almost all standard HTTP status codes with meaningful names.
- **HttpStatus** implements **HttpStatusCode**, so we can use it wherever **HttpStatusCode** is expected.

##### 🟦 1xx (Informational)

- `HttpStatus.CONTINUE`

##### 🟦 2xx (Success)

- `HttpStatus.OK`
- `HttpStatus.CREATED`

##### 🟦 3xx (Redirection)

- `HttpStatus.FOUND`
- `HttpStatus.NOT_MODIFIED`

##### 🟦 4xx (Client Error)

- `HttpStatus.BAD_REQUEST`
- `HttpStatus.UNAUTHORIZED`
- `HttpStatus.FORBIDDEN`
- `HttpStatus.NOT_FOUND`

##### 🟦 5xx (Server Error)

- `HttpStatus.INTERNAL_SERVER_ERROR`
- `HttpStatus.BAD_GATEWAY`
- `HttpStatus.SERVICE_UNAVAILABLE`
- `HttpStatus.GATEWAY_TIMEOUT`

### ➡️ HttpStatusCode

- HttpStatusCode is an interface introduced in Spring 6 / Spring Boot 3.
- Modern and Flexible, It allows custom status codes (not just the ones in the enum) using `HttpStatusCode.valueOf(499)` or similar.
- Use **HttpStatus** when you want readable names (`HttpStatus.CREATED`).
- Use **HttpStatusCode** (or `HttpStatusCode.valueOf(...)`) when you need flexibility or in newer Spring Boot 3+ code.

##### 🟦 value()

- Returns the actual **integer value** of the status code(e.g., 200)

```java
HttpStatusCode status = HttpStatus.OK; // or ResponseEntity.getStatusCode()
int code = status.value(); // Returns 200

System.out.println(code);  // Output: 200
```

- ###### 🔵 Real-world example

```java
ResponseEntity<User> response = restClient.get()
        .uri("/users/123")
        .retrieve()
        .toEntity(User.class);

int statusCode = response.getStatusCode().value();

if (statusCode == 200) {
    // process success
} else if (statusCode >= 400) {
    // handle error
}
```

##### 🟦 is1xxInformational(), is2xxSuccessful(), is3xxRedirection(), is4xxClientError(), is5xxServerError()

```java
HttpStatusCode status = HttpStatus.NOT_FOUND;   // 404

System.out.println(status.is2xxSuccessful());   // false
System.out.println(status.is4xxClientError());  // true
System.out.println(status.is5xxServerError());  // false
```

- ###### 🔵 Real-world example
  - Practical usage in exception handler or response processing:

```java
HttpStatusCode statusCode = response.getStatusCode();

if (statusCode.is2xxSuccessful()) {
    // Everything is fine - process the body
    log.info("Request succeeded");
}
else if (statusCode.is4xxClientError()) {
    log.warn("Client made a bad request: {}", statusCode.value());
    // Maybe throw a custom validation exception
}
else if (statusCode.is5xxServerError()) {
    log.error("Server error occurred: {}", statusCode.value());
    // Trigger alert or retry logic
}
```

##### 🟦 isError()

- A convenient shortcut that returns true if the status code is either 4xx or 5xx.
- It is equivalent to:

```java
status.is4xxClientError() || status.is5xxServerError()`
```

- ###### 🔵 Real-world example

```java
HttpStatusCode status = HttpStatus.BAD_REQUEST;   // 400

if (status.isError()) {
    // This block will execute for all error codes
    handleErrorResponse(status);
}
```

##### 🟦 isSameCodeAs(other)

- Checks whether two **HttpStatusCode** objects represent the exact same numeric status code.
- This is especially useful when comparing a custom status code or when working with **HttpStatusCode** from different sources.

```java
HttpStatusCode status1 = HttpStatus.OK;                    // 200
HttpStatusCode status2 = HttpStatusCode.valueOf(200);      // Custom way to create 200
HttpStatusCode status3 = HttpStatus.CREATED;               // 201

System.out.println(status1.isSameCodeAs(status2));   // true
System.out.println(status1.isSameCodeAs(status3));   // false
```

- ###### 🔵 Real-world example

```java
HttpStatusCode expected = HttpStatus.CREATED;
HttpStatusCode actual = response.getStatusCode();

if (actual.isSameCodeAs(expected)) {
    log.info("Resource created successfully");
} else {
    log.warn("Unexpected status: expected 201 but got {}", actual.value());
}
```

### ➡️ ResponseEntity

- Static Convenience Methods

##### 🟦 200

- `ResponseEntity.ok() or ResponseEntity.ok(body)`
- `ResponseEntity.created(location)`
- `ResponseEntity.accepted()`

##### 🟦 300

- `ResponseEntity.found()`

##### 🟦 400

- `ResponseEntity.noContent()`
- `ResponseEntity.notFound()`

##### 🟦 500

- `ResponseEntity.Internal_Server_Error`
- `ResponseEntity.Service_Unavailable`

##### 🟦 General method for any status

- `ResponseEntity.status(HttpStatus.CREATED)`
  or
- `ResponseEntity.status(201)`
  or
- `ResponseEntity.status(HttpStatusCode.valueOf(422))`
