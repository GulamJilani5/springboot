⏺️ ➡️ 🟦 🟩 🟢 🔵 🔷 🔹🔴 ☑️ ✔️ ✓→•←⁕⁂※⁜‣

# ⏺️ HTTP Headers
- HTTP headers are key-value pairs sent in the HTTP message (both requests and responses) after the request/response line and before the body.
- They provide metadata — information about the message, the sender, the recipient, how to handle the content, security policies, caching rules, etc.
- They describe:
   - How to interpret the response
   - Security rules
   - Caching behavior
   - Content type
   - Authentication info
   - Client behavior instructions

### ➡️ Security Headers
- These are non-negotiable in real apps.
- These headers are often set globally using filters/interceptors, not per controller.
##### 🟦 Authorization
- Used for sending tokens (`JWT`, `OAuth`)
```text
Authorization: Bearer eyJhbGciOiJIUzI1NiIs...
```
- Mostly used in request, but sometimes returned in response(either in body or header) (token refresh).

##### 🟦 Set-Cookie
- Backend sends session/cookie to browser
```text
Set-Cookie: JSESSIONID=abc123; HttpOnly; Secure
```
- Used for:
  - Session management
  - Refresh tokens
 
##### 🟦 Strict-Transport-Security (HSTS)
- Forces HTTPS only 
```text
Strict-Transport-Security: max-age=31536000; includeSubDomains
```
##### 🟦 X-Content-Type-Options
```text
X-Content-Type-Options: nosniff
```
- Prevents MIME type attacks

##### 🟦 X-Frame-Options
```text
X-Frame-Options: DENY
```
- Prevents clickjacking

##### 🟦 Content-Security-Policy (CSP)
```text
Content-Security-Policy: default-src 'self'
```
- Prevents XSS attacks

### ➡️ Content Headers
- These define what data you are sending
##### 🟦 Content-Type
- Common values:
  - application/json
  - application/xml
  - text/html
  - multipart/form-data

```text
Content-Type: application/json
```
##### 🟦 Content-Length
- Size of response body
```text
Content-Length: 348
```
##### 🟦 Content-Encoding
- Compression
```text
Content-Encoding: gzip
```
##### 🟦 Content-Disposition
- Used for file download
```text
Content-Disposition: attachment; filename="report.pdf"
```

### ➡️ Caching Headers (PERFORMANCE CRITICAL)
- Real use:
  - Reduce API calls
  - Improve frontend performance
##### 🟦 Cache-Control
```text
Cache-Control: no-cache, no-store, must-revalidate
```
- Options:
  - no-cache
  - no-store
  - max-age=3600

##### 🟦 ETag
```text
ETag: "abc123"
```
- Used for smart caching

##### 🟦 Last-Modified
```text
Used for smart caching
```

### ➡️ CORS Headers (FRONTEND-BACKEND COMMUNICATION)
- Without this → React frontend cannot call backend
##### 🟦 Access-Control-Allow-Origin
```text
Access-Control-Allow-Origin: http://localhost:3000
```
##### 🟦 Access-Control-Allow-Methods
```text
Access-Control-Allow-Methods: GET, POST, PUT, DELETE
```
##### 🟦 Access-Control-Allow-Headers
```text
Access-Control-Allow-Headers: Authorization, Content-Type
```

### ➡️ Custom Headers (BUSINESS LOGIC)

- Used in:
  - Logging
  - Debugging
  - Distributed systems (microservices) 

```text
X-Request-Id: 12345
X-Trace-Id: abc-xyz
X-App-Version: 1.0
```

### ➡️ How to Return Headers in Spring Boot

##### 🟦 Using ResponseEntity (BEST PRACTICE)
```java
@GetMapping("/user")
public ResponseEntity<String> getUser() {

    HttpHeaders headers = new HttpHeaders();
    headers.add("X-Request-Id", "12345");
    headers.add("Cache-Control", "no-cache");

    return ResponseEntity
            .ok()
            .headers(headers)
            .body("User fetched successfully");
}
```
##### 🟦 Returning File with Headers
```java
@GetMapping("/download")
public ResponseEntity<byte[]> downloadFile() {

    HttpHeaders headers = new HttpHeaders();
    headers.add("Content-Disposition", "attachment; filename=report.pdf");

    return ResponseEntity
            .ok()
            .headers(headers)
            .body(fileBytes);
}
```
##### 🟦 Setting Cookie
```java
@GetMapping("/login")
public ResponseEntity<String> login(HttpServletResponse response) {

    Cookie cookie = new Cookie("sessionId", "abc123");
    cookie.setHttpOnly(true);
    cookie.setSecure(true);

    response.addCookie(cookie);

    return ResponseEntity.ok("Logged in");
}
```
##### 🟦 Global Header Configuration (REAL WORLD)
```java
@Component
public class SecurityHeaderFilter implements Filter {

    public void doFilter(...) {
        response.setHeader("X-Frame-Options", "DENY");
        response.setHeader("X-Content-Type-Options", "nosniff");
        chain.doFilter(request, response);
    }
}
```

