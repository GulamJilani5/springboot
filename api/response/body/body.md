### ➡️ Body

The response body contains the data returned to the client, such as resources, error messages, or metadata.

##### 🟦 What Can Be Sent:

JSON: Most common for REST APIs (e.g., a user object or list).
XML: For legacy systems.
Text: For simple responses.
Binary Data: For files (e.g., images, PDFs).

##### 🟦 How to Send in Spring Boot:

- 🔵 **JSON:**

```java
@GetMapping("/users")
public User getUser() {
return new User("john_doe", "john@example.com");
}
Spring Boot automatically serializes the User object to JSON with Content-Type: application/json.
File Download:
javaimport org.springframework.core.io.ByteArrayResource;
import org.springframework.http.HttpHeaders;
import org.springframework.http.MediaType;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class FileController {
@GetMapping("/download")
public ResponseEntity<ByteArrayResource> downloadFile() {
byte[] fileContent = "Sample file content".getBytes();
ByteArrayResource resource = new ByteArrayResource(fileContent);

        HttpHeaders headers = new HttpHeaders();
        headers.add(HttpHeaders.CONTENT_DISPOSITION, "attachment; filename=sample.txt");

        return ResponseEntity.ok()
                .headers(headers)
                .contentType(MediaType.APPLICATION_OCTET_STREAM)
                .body(resource);
    }

}
```

- **🔷Error Response:**

```java

@GetMapping("/users/{id}")
public ResponseEntity<?> getUser(@PathVariable Long id) {
if (id == null) {
return ResponseEntity.status(HttpStatus.BAD_REQUEST)
.body(new ErrorResponse("Invalid user ID"));
}
return ResponseEntity.ok(new User("john_doe"));
}
```

##### 🟦 Constraints:

- Ensure the Content-Type matches the body format.
- Large responses may require pagination or streaming.
- Use appropriate status codes (e.g., 204 No Content for empty responses).
