⏺️ ➡️ 🟦 🟩 🟢 🔵 🔷 🔹🔴 ☑️ ✔️ ✓→•←⁕⁂※⁜‣

# ⏺️ @RequestBody, @RequestHeader, @PathVariable & @RequestParam

### ➡️ @RequestBody

- JSON body → Java Object (DTO)

##### 🟦 Request(JSON)

```json
POST /users
{
  "name": "John",
  "email": "john@example.com"
}
```

##### 🟦 DTO

```java
public class UserRequestDTO {
    private String name;
    private String email;
}
```

##### 🟦 Controller

```java
@PostMapping("/users")
public ResponseEntity<String> createUser(@RequestBody UserRequestDTO dto) {
    return ResponseEntity.ok("User: " + dto.getName());
}
```

- Uses **Jackson** internally
- Requires `Content-Type: application/json`

##### 🟦 Works with validation

```java
public ResponseEntity<String> createUser(@Valid @RequestBody UserRequestDTO dto)
```

### ➡️ @RequestHeader

##### 🟦 Header Example

```text
Authorization: Bearer token123
User-Agent: Chrome
```

##### 🟦 Controller

```java
@GetMapping("/header")
public ResponseEntity<String> getHeader(
        @RequestHeader("Authorization") String authToken) {
    return ResponseEntity.ok("Token: " + authToken);
}
```

##### 🟦 Optional header

```java
@RequestHeader(required = false) String Authorization
```

##### 🟦 Default value

```java
@RequestHeader(defaultValue = "unknown") String client
```

### ➡️ @PathVariable

- Value from URL path

```text
GET /users/101
```

```java
@GetMapping("/users/{id}")
public ResponseEntity<String> getUser(@PathVariable("id") Long id) {
    return ResponseEntity.ok("User ID: " + userId);
}
```

##### 🟦 Can rename variable

```java
@PathVariable("id") Long userId
```

##### 🟦 Optional case

```java
@PathVariable(required = false) Long id
```

### ➡️ @RequestParam

- Reads value from query parameters.

##### 🟦 RequestParam Example

```text
GET /users?page=1&size=10
```

##### 🟦 Controller

```java
@GetMapping("/users")
public ResponseEntity<String> getUsers(
        @RequestParam int page,
        @RequestParam int size) {
    return ResponseEntity.ok("Page: " + page + ", Size: " + size);
}
```

##### 🟦 Optional param

```java
@RequestParam(required = false) String sort
```

##### 🟦 Default value

```java
@RequestParam(defaultValue = "0") int page
```

##### 🟦 Rename param

```java
@RequestParam("p") int page
```
