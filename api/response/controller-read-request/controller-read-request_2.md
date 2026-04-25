⏺️ ➡️ 🟦 🟩 🟢 🔵 🔷 🔹🔴 ☑️ ✔️ ✓→•←⁕⁂※⁜‣

# ⏺️ RequestEntity<T>

- RequestEntity<T> = Complete HTTP Request Wrapper
- **It wraps:**
  - HTTP Method
  - The body (deserialized to your DTO/POJO)
  - All HTTP headers (HttpHeaders)
  - The URL (if needed)

### ➡️ Http Methods

- Returns an **Enum** 🔴 of type `org.springframework.http.HttpMethod`.
- Possible values: `GET`, `POST`, `PUT`, `DELETE`, `PATCH`, `OPTIONS`, `HEAD`

```java
HttpMethod httpMethod = requestEntity.getMethod();
String methodName = (httpMethod != null) ? httpMethod.name() : "UNKNOWN";

// httpMethod.name() `GET`, `POST`, `PUT`, `DELETE`, `PATCH`
System.out.println("HTTP Method: " + methodName);

// Example conditional logic
if (HttpMethod.POST.equals(httpMethod)) {
    // handle creation logic
} else if (HttpMethod.GET.equals(httpMethod)) {
    // handle read logic
}
```

##### HtttpMethod vs httpMethod.name()

- **HttpMethod** is a **Java Enum** provided by Spring Framework.
  - `import org.springframework.http.HttpMethod;`
  - HttpMethod.POST, HttpMethod.GET, HttpMethod.PUT, HttpMethod.PATCH, HttpMethod.DELETE,
- httpMethod.name()

```java
HttpMethod method = requestEntity.getMethod();   // Returns the enum
httpMethod.name()
```

### ➡️ body

- The actual payload/data sent by the client in the HTTP request
  - JSON, XML, form data, etc.

```java
UserRegistrationRequest body = requestEntity.getBody();
if (body != null) {
    System.out.println("Name: " + body.getName());
    System.out.println("Email: " + body.getEmail());
}
```

### ➡️ Headers

- `org.springframework.http.HttpHeaders`
- Key-value metadata sent by the client
  - `Authorization`, `Content-Type`, `X-Request-Id`, `custom headers`, `cookies`, etc.
- **Tip:** Use `headers.getFirst(key)` for most cases as it returns the first (and usually only) value.
- Headers are always available (even if empty). 🔴

```java
HttpHeaders headers = requestEntity.getHeaders();

// Get single header value
String authToken = headers.getFirst("Authorization");
String clientId   = headers.getFirst("X-Client-Id");

// Get all values for a header (rarely needed)
List<String> values = headers.get("Authorization");

// Get Content-Type
MediaType contentType = headers.getContentType();

// Get all headers as Map
Map<String, List<String>> allHeaders = headers.toSingleValueMap(); // or headers
```

### ➡️ Urls

- The complete address the client requested:
  - protocol, host, port, path, and query parameters.

```java
URI url = requestEntity.getUrl();

String fullUrl   = url.toString();           // Complete URL
String scheme    = url.getScheme();          // "http" or "https"
String host      = url.getHost();            // "localhost" or "api.example.com"
int port         = url.getPort();            // 8080 or -1 (default)
String path      = url.getPath();            // "/api/register"
String query     = url.getQuery();           // "param1=value1&param2=value2" (null if none)
String fragment  = url.getFragment();        // anchor part after #
```

```java
import org.springframework.http.HttpHeaders;
import org.springframework.http.HttpMethod;
import org.springframework.http.RequestEntity;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import java.net.URI;

@RestController
@RequestMapping("/api")
public class UserController {

    @PostMapping("/register")
    public ResponseEntity<String> registerUser(RequestEntity<UserRegistrationRequest> requestEntity) {

        // 1. Access Headers
        HttpHeaders headers = requestEntity.getHeaders();
        String authToken = headers.getFirst("Authorization");
        String clientId = headers.getFirst("X-Client-Id");
        String contentType = headers.getContentType() != null ?
                             headers.getContentType().toString() : "unknown";

        // 2. Access the Body
        UserRegistrationRequest body = requestEntity.getBody();

        // 3. Get HTTP Method (GET, POST, PUT, etc.)
        HttpMethod httpMethod = requestEntity.getMethod();
        String methodName = httpMethod != null ? httpMethod.name() : "UNKNOWN";

        // 4. Get the Full URL (as URI)
        URI url = requestEntity.getUrl();           // Full URL including scheme, host, port, path, query
        String fullUrl = url != null ? url.toString() : "unknown";
        // We can also extract parts of the URL if needed:
        String scheme = url.getScheme();            // http or https
        String host = url.getHost();                // e.g., localhost or example.com
        int port = url.getPort();                   // port number (-1 if not specified)
        String path = url.getPath();                // e.g., /api/register
        String query = url.getQuery();              // query string if any

        // Logging / Debugging
        System.out.println("HTTP Method     : " + methodName);
        System.out.println("Full URL        : " + fullUrl);
        System.out.println("Path            : " + path);
        System.out.println("Auth Token      : " + authToken);
        System.out.println("Client ID       : " + clientId);
        System.out.println("User from body  : " + (body != null ? body.getName() + " - " + body.getEmail() : "null"));

        // business logic here...

        return ResponseEntity.ok("User registered successfully");
    }
}
```
