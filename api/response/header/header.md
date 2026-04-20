### ➡️ Headers

The server can send headers to provide metadata, control caching, or manage authentication.

##### 🟦 What Can Be Sent:

- **Content-Type:** Specifies the response body format (e.g., `application/json`).
- **Set-Cookie:** Sends cookies to the client for session management.
- **Location:** Provides the URL of a newly created resource (e.g., after a `POST`).
- **Cache-Control:** Controls caching (e.g., `no-cache`, `max-age=3600`).
- **Access-Control-Allow- Headers\*:** For CORS (e.g., `Access-Control-Allow-Origin`, `Access-Control-Allow-Credentials`).
- **Custom Headers:** For API-specific metadata (e.g., `X-Rate-Limit: 100`).

##### 🟦 How to Send in Spring Boot:

- **🔷Using ResponseEntity:**

```java

javaimport org.springframework.http.HttpHeaders;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class UserController {
@GetMapping("/users")
public ResponseEntity<User> getUser() {

HttpHeaders headers = new HttpHeaders();
headers.add("Content-Type", "application/json");
headers.add("X-Custom-Header", "value");
headers.add("Cache-Control", "max-age=3600");

        User user = new User("john_doe");
        return new ResponseEntity<>(user, headers, HttpStatus.OK);
    }

}
```

- **🔷Using @ResponseHeader:**

```java
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.ResponseHeader;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class UserController {
@GetMapping("/users")
@ResponseHeader(name = "X-Custom-Header", value = "value")
public User getUser() {
return new User("john_doe");
}
}

```

##### 🟦 Constraints:

- Ensure CORS headers are set for cross-origin requests.
- Avoid sensitive data in headers (e.g., tokens should be in HttpOnly cookies or body).
- Large headers can impact performance.
