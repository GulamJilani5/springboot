⏺️ ➡️ 🟦 🟩 🟢 🔵 🔷 🔹🔴 ☑️ ✔️ ✓→•←⁕⁂※⁜‣

# ⏺️ Spring Boot → Database → ACOS Flow

```text
                    ┌──────────────────────────┐
                    │     Postman / Client     │
                    │  GET /api/claims/{id}/   │
                    │       acos-values        │
                    └────────────┬─────────────┘
                                 │
                                 ▼
                    ┌──────────────────────────┐
                    │    ClaimsController     │
                    │ ClaimsController.java   │
                    └────────────┬─────────────┘
                                 │
                                 ▼
                    ┌──────────────────────────┐
                    │      ClaimsService      │
                    │    ClaimsService.java   │
                    └────────────┬─────────────┘
                                 │
                                 ▼
                    ┌──────────────────────────┐
                    │     ClaimRepository     │
                    │    ClaimRepository.java │
                    └────────────┬─────────────┘
                                 │
                                 ▼
                    ┌──────────────────────────┐
                    │        Database         │
                    │       ClaimEntity       │
                    │                          │
                    │ Returns claimId and     │
                    │ customerId              │
                    └────────────┬─────────────┘
                                 │
                                 ▼
                    ┌──────────────────────────┐
                    │      ClaimsService      │
                    │                          │
                    │ Maps DB data into       │
                    │ AcosRequest DTO         │
                    └────────────┬─────────────┘
                                 │
                                 ▼
                    ┌──────────────────────────┐
                    │       AcosClient        │
                    │      AcosClient.java    │
                    │                          │
                    │ Adds API key and        │
                    │ Application ID headers  │
                    │                          │
                    │ Calls RestTemplate      │
                    └────────────┬─────────────┘
                                 │
                                 │ HTTP POST
                                 │ JSON body + headers
                                 ▼
                    ┌──────────────────────────┐
                    │     External ACOS API   │
                    │                          │
                    │ Processes request       │
                    │ Returns wrapped JSON    │
                    └────────────┬─────────────┘
                                 │
                                 │ HTTP response
                                 ▼
                    ┌──────────────────────────┐
                    │      AcosResponse       │
                    │     AcosResponse.java   │
                    │                          │
                    │ status, message, data[] │
                    └────────────┬─────────────┘
                                 │
                                 ▼
                    ┌──────────────────────────┐
                    │    ClaimsController     │
                    │                          │
                    │ Returns response as JSON│
                    └────────────┬─────────────┘
                                 │
                                 ▼
                    ┌──────────────────────────┐
                    │     Postman / Client    │
                    │    Receives JSON result │
                    └──────────────────────────┘

```

# ⏺️ Folder Structure

```text
acos-demo/
├── pom.xml
└── src/main/
    ├── java/com/example/
    │   ├── MyApplication.java
    │   ├── config/
    │   │   ├── AcosApiProperties.java
    │   │   └── RestTemplateConfig.java
    │   ├── entity/
    │   │   └── ClaimEntity.java
    │   ├── repository/
    │   │   └── ClaimRepository.java
    │   ├── client/
    │   │   └── AcosClient.java
    │   ├── service/
    │   │   └── ClaimsService.java
    │   ├── dto/
    │   │   ├── AcosRequest.java
    │   │   └── AcosResponse.java
    │   └── controller/
    │       └── ClaimsController.java
    └── resources/
        ├── application.properties
        └── data.sql
```

### ➡️ resources/application.properties

```java
# ACOS configuration
acos.api.base-url=https://acos.example.com
acos.api.api-key=${ACOS_API_KEY}
acos.api.application-id=my-application-123
```

### ➡️ MyApplication.java

```java
@SpringBootApplication
@ConfigurationPropertiesScan
public class MyApplication {

    public static void main(String[] args) {
        SpringApplication.run(MyApplication.class, args);
    }
}
```

### ➡️ config/AcosApiProperties.java

```java
@ConfigurationProperties(prefix = "acos.api")
public class AcosApiProperties {

    private String baseUrl;
    private String apiKey;
    private String applicationId;

    //getter
    //setter
}
```

### ➡️ config/RestTemplateConfig.java

```java
@Configuration
public class RestTemplateConfig {

    @Bean
    public RestTemplate restTemplate() {
        return new RestTemplate();
    }
}
```

### ➡️ entity/ClaimEntity.java

```java
@Entity
@Table(name = "claims")
public class ClaimEntity {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(name = "claim_id", nullable = false, unique = true)
    private String claimId;

    @Column(name = "customer_id", nullable = false)
    private String customerId;

    //getter
    //setter
}
```

### ➡️ dto/AcosRequest.java

```java
public class AcosRequest {

    private String claimId;
    private String customerId;

    public String getClaimId() {
        return claimId;
    }

    //getter
    //setter

}
```

### ➡️ dto/AcosResponse.java

```java
public class AcosResponse {

    private String status;
    private String message;
    private List<String> data;
    //getter
    //setter
}
```

- ACOS returns a wrapped JSON response:

```text
{
  "status": "SUCCESS",
  "message": "Values fetched successfully",
  "data": ["VALUE_A", "VALUE_B", "VALUE_C"]
}
```

### ➡️ controller/ClaimsController.java

```java
@RestController
@RequestMapping("/api/claims")
public class ClaimsController {

    private final ClaimsService claimsService;

    public ClaimsController(ClaimsService claimsService) {
        this.claimsService = claimsService;
    }

    @GetMapping("/{claimId}/acos-values")
    public AcosResponse getAcosValues(@PathVariable String claimId) {
        return claimsService.fetchAcosValues(claimId);
    }
}
```

### ➡️ service/ClaimsService.java

- It does
  - fetches database details
  - constructs the ACOS request
  - and returns the ACOS response.

```java
@Service
public class ClaimsService {

    private final ClaimRepository claimRepository;
    private final AcosClient acosClient;

    public ClaimsService(ClaimRepository claimRepository, AcosClient acosClient) {
        this.claimRepository = claimRepository;
        this.acosClient = acosClient;
    }

    public AcosResponse fetchAcosValues(String claimId) {

        ClaimEntity claim = claimRepository.findByClaimId(claimId)
                            .orElseThrow(() -> new EntityNotFoundException( "Claim not found: " + claimId));

        AcosRequest request = new AcosRequest();
        request.setClaimId(claim.getClaimId());
        request.setCustomerId(claim.getCustomerId());

        return acosClient.fetchValues(request);
    }
}
```

### ➡️ repository/ClaimRepository.java

```java
public interface ClaimRepository extends JpaRepository<ClaimEntity, Long> {
    Optional<ClaimEntity> findByClaimId(String claimId);
}
```

### ➡️ client/AcosClient.java

- This class is responsible for the HTTP call to ACOS, including the headers, request body, and wrapped response.

```java
@Component
public class AcosClient {

    private final RestTemplate restTemplate;
    private final AcosApiProperties properties;

    public AcosClient(RestTemplate restTemplate, AcosApiProperties properties) {
        this.restTemplate = restTemplate;
        this.properties = properties;
    }

    public AcosResponse fetchValues(AcosRequest requestBody) {

        HttpHeaders headers = new HttpHeaders();
        headers.setContentType(MediaType.APPLICATION_JSON);
        headers.set("x-api-key", properties.getApiKey());
        headers.set("application-id", properties.getApplicationId());

        HttpEntity<AcosRequest> request = new HttpEntity<>(requestBody, headers);

        ResponseEntity<AcosResponse> response = restTemplate.exchange(
                        properties.getBaseUrl() + "/values",
                        HttpMethod.POST,
                        request,
                        AcosResponse.class
                );

        return Objects.requireNonNull(
                response.getBody(),
                "ACOS returned an empty response body"
        );
    }
}
```
