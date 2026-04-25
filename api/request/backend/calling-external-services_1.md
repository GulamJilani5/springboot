⏺️ ➡️ 🟦 🟩 🟢 🔵 🔷 🔹🔴 ☑️ ✔️ ✓→•←⁕⁂※⁜‣

# ⏺️ API Calls from the Backend (Spring Boot - RestTemplate)

- In backend (Spring Boot), API calls to external services are made using RestTemplate (or WebClient).
- The components of an API call include **URL**, **HTTP Method**, **headers**, **body**, **authentication**, and **additional configurations** — similar to frontend but controlled programmatically.

### ➡️ URL

- `https://external-api.com/users?role=admin&page=2`
- Protocol → http / https
- Host → external-api.com
- Path → /users
- Query Params → ?role=admin&page=2

```java
String url = "https://external-api.com/users?role=admin&page=2";
```

### ➡️

| Method  | Purpose               |
| ------- | --------------------- |
| GET     | Fetch data            |
| POST    | Create                |
| PUT     | Full update           |
| PATCH   | Partial update        |
| DELETE  | Remove                |
| OPTIONS | Check allowed methods |
| HEAD    | Only headers          |

```java
HttpMethod.GET
HttpMethod.POST
```

### ➡️ Headers

- Headers are used to provide metadata, authentication, or content specifications.

##### 🟦 Content-Type

- Specifies request body format.

```java
Content-Type: application/json
Content-Length
```

##### 🟦 Authorization

- Used for authentication with external services.

```java
Authorization: Bearer <token>
Authorization: Basic <base64>
```

##### 🟦 Service Info (instead of browser info)

- No User-Agent/Origin automatically like browser
- Can be manually set:

```java
User-Agent: My-Service
```

##### 🟦 Accept

- Expected response format.

```java
Accept: application/json
```

##### 🟦 Custom Headers

```java
X-Request-ID
X-Tenant-ID
X-API-Key
Correlation-ID
```

##### 🟦 Caching

```java
Caching
```

### ➡️ Body

- The body contains the data sent to the external service.

##### 🟦 JSON (Most Common)

```java
String requestBody = "{\"username\":\"john_doe\"}";
```

##### 🟦 Object → JSON (Recommended)

```java
UserRequest request = new UserRequest("john_doe");
```

##### 🟦 Form Data

```java
MultiValueMap<String, String> formData = new LinkedMultiValueMap<>();
formData.add("username", "john_doe");
```

##### 🟦 Binary / File

```java
ByteArrayResource resource = new ByteArrayResource(fileBytes);
```

##### 🟦 Constraints

- No browser CORS restrictions
- Full control over headers
- Security handled at backend level

### ➡️ Additional Configurations

##### 🟦 Timeout

- Configured via RestTemplate bean

```java
SimpleClientHttpRequestFactory factory = new SimpleClientHttpRequestFactory();
factory.setConnectTimeout(5000);
factory.setReadTimeout(5000);

RestTemplate restTemplate = new RestTemplate(factory);
```

##### 🟦 Error Handling

```java
try {
    restTemplate.exchange(...);
} catch (HttpClientErrorException e) {
    // 4xx
} catch (HttpServerErrorException e) {
    // 5xx
}
```

##### 🟦 Interceptors (Logging / Security)

```java
restTemplate.getInterceptors().add((request, body, execution) -> {
    // logging
    return execution.execute(request, body);
});
```

##### 🟦 Retry / Circuit Breaker (Advanced)

- Resilience4j / RetryTemplate

### ➡️ Example

```text
Frontend (React JSON)
   ↓
Controller (@RequestBody)
   ↓
UserRequestDTO (internal)
   ↓
Servic

   ↓
Mapper
   ↓
ExternalUserRequestDTO
   ↓
RestTemplate call
   ↓
External API
```

##### 🟦 DTO

```java
import java.time.LocalDate;

public class UserRequestDTO {

    private String username;
    private LocalDate dob;
    private String mobileNumber;

    // Constructor
    public UserRequestDTO(String username, LocalDate dob, String mobileNumber) {
        this.username = username;
        this.dob = dob;
        this.mobileNumber = mobileNumber;
    }

    // Getters & Setters
    // Equals and hashcode
}
```

##### 🟦 External DTO (Backend → External API)

```java
package com.example.external.dto;

public class ExternalUserRequestDTO {

    private String user_name;
    private String date_of_birth;
    private String phone_number;

    public ExternalUserRequestDTO(String user_name, String date_of_birth, String phone_number) {
        this.user_name = user_name;
        this.date_of_birth = date_of_birth;
        this.phone_number = phone_number;
    }

    // getters & setters
}
```

##### 🟦 Mapper Layer

```java
package com.example.mapper;

import com.example.dto.UserRequestDTO;
import com.example.external.dto.ExternalUserRequestDTO;

public class UserMapper {

    public static ExternalUserRequestDTO toExternal(UserRequestDTO dto) {
        return new ExternalUserRequestDTO(
                dto.getUsername(),
                dto.getDob().toString(),   // format conversion
                dto.getMobileNumber()
        );
    }
}
```

##### 🟦 Controller

```java
package com.example.controller;

import com.example.dto.UserRequestDTO;
import com.example.service.UserService;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/users")
public class UserController {

    private final UserService userService;

    public UserController(UserService userService) {
        this.userService = userService;
    }

    @PostMapping("/create")
    public ResponseEntity<String> createUser(@RequestBody UserRequestDTO userRequestDTO) {
        return userService.createUser(userRequestDTO);
    }
}
```

##### 🟦 Service

```java
package com.example.service;

import com.example.dto.UserRequestDTO;
import com.example.external.dto.ExternalUserRequestDTO;
import com.example.mapper.UserMapper;
import org.springframework.http.*;
import org.springframework.stereotype.Service;
import org.springframework.web.client.RestTemplate;

@Service
public class UserService {

    private final RestTemplate restTemplate = new RestTemplate();

    public ResponseEntity<String> createUser(UserRequestDTO userRequestDTO) {

        String url = "https://external-api.com/users";

        // 🔐 Headers
        HttpHeaders headers = new HttpHeaders();
        headers.setContentType(MediaType.APPLICATION_JSON);
        headers.setBearerAuth("your-token-here");
        headers.set("X-Request-ID", "12345");

        // 🔁 Body - Mapping (internal → external)
        ExternalUserRequestDTO externalDto = UserMapper.toExternal(userRequestDTO);

        // 📦 HttpEntity
        HttpEntity<ExternalUserRequestDTO> entity =
                new HttpEntity<>(externalDto, headers);

        // 🌍 External API Call
        ResponseEntity<String> response = restTemplate.exchange(
                url,
                HttpMethod.POST,
                entity,
                String.class
        );

        return response;
    }
}
```
