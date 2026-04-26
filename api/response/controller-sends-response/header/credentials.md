⏺️ ➡️ 🟦 🟩 🟢 🔵 🔷 🔹🔴 ☑️ ✔️ ✓→•←⁕⁂※⁜‣

# ⏺️ Credentials
- The server can send credentials or manage authentication in responses.

### ➡️ Cookies

- For session-based authentication (Set-Cookie header).

```java

import javax.servlet.http.Cookie;
@RestController
public class AuthController {
@GetMapping("/login")
public ResponseEntity<String> login() {
Cookie cookie = new Cookie("sessionId", "abc123");
cookie.setHttpOnly(true);
cookie.setSecure(true);
cookie.setMaxAge(3600);

        HttpHeaders headers = new HttpHeaders();
        headers.add(HttpHeaders.SET_COOKIE, cookie.toString());

        return new ResponseEntity<>("Login successful", headers, HttpStatus.OK);
    }
}
```


##### 🟦 Constraints:

Use HttpOnly and Secure flags for cookies to prevent XSS and ensure HTTPS.
Avoid sending sensitive data in headers unless necessary.
Include CORS headers (Access-Control-Allow-Credentials) for cookie-based auth.



### ➡️ Tokens
- Returned in the body (**e.g.**, JWT after login) or headers.
- Token can be send in Body or header.
- Here it is send in body

```java
  @PostMapping("/login")
  public ResponseEntity<Map<String, String>> login(@RequestBody LoginRequest request) {
  String token = generateJwtToken(request.getUsername());
  Map<String, String> response = Map.of("token", token);
  return ResponseEntity.ok(response);
  }
```



### ➡️ CORS Configuration:
- For protecting against cross-site request forgery.

```java
javaimport org.springframework.web.bind.annotation.CrossOrigin;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
@CrossOrigin(origins = "http://localhost:3000", allowCredentials = "true")
public class UserController {
@GetMapping("/users")
public List<User> getUsers() {
return List.of(new User("john_doe"));
}
}

```

### ➡️ Pagination:

- Use headers or body to provide pagination metadata.

```java

@GetMapping("/users")
public ResponseEntity<List<User>> getUsers(@RequestParam int page, @RequestParam int size) {
HttpHeaders headers = new HttpHeaders();
headers.add("X-Total-Count", "100");
headers.add("Link", "<next_page_url>; rel=\"next\"");

    List<User> users = fetchUsers(page, size);
    return new ResponseEntity<>(users, headers, HttpStatus.OK);

}

```

### ➡️ Rate Limiting:

- Use headers to inform clients of limits.

```java
    headers.add("X-Rate-Limit-Remaining", "99");
    headers.add("X-Rate-Limit-Reset", "1631234567");
```
