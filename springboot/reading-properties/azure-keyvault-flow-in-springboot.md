⏺️ ➡️ 🟦 🔵🔹🔷 🔵 ☑️ ✔️ 🔴 ⭕ • ‣ → ⁕

# ⏺️ How Spring Boot reads the value from the

### ➡️ Azure Key Vault Integration With Spring Boot

##### 🟦 Step 1: Spring Boot starts

```java
@SpringBootApplication
@ConfigurationPropertiesScan
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

- Spring Boot starts creating the ApplicationContext (IoC Container).

##### 🟦 Step 2: Spring reads dependencies

- pom.xml

```java
<dependency>
    <groupId>com.azure.spring</groupId>
    <artifactId>spring-cloud-azure-starter-keyvault-secrets</artifactId>
</dependency>
```

- Spring Boot sees this dependency. Because of **AutoConfiguration** (`@EnableAutoConfiguration`).
- It says: "Azure Key Vault starter is present. Let me configure Azure Key Vault automatically."
- No code written by developers.

##### 🟦 Step 3: Spring Reads Configuration from application.properties:

```java
# Azure Key Vault endpoint
spring.cloud.azure.keyvault.secret.property-sources[0].endpoint=https://<your-vault-name>.vault.azure.net/

# Map Key Vault secrets to ACOS configuration properties
acos.api-key=${acos-api-key}
acos.application-id=${acos-application-id}
acos.url=${acos-url}
```

- Now Spring knows:
  - Which Key Vault?
  - Which Azure Tenant?
  - Which authentication method?

##### 🟦 Step 4: Authentication

- Spring now authenticates to Azure
- Managed Identity (Azure VM/AKS)
- No username/password.

```text
Application
      ↓
Managed Identity
      ↓
Azure AD
      ↓
Access Token
```

- Service Principal

```text
Client ID
Client Secret
Tenant ID
      ↓
Azure AD
      ↓
Access Token
```

##### 🟦 Step 5: Azure verifies identity

- Azure AD/Entra Id checks
- Is this application allowed?
- If yes returns `OAuth Access Token`

##### 🟦 Step 6: Spring calls Azure Key Vault

- Spring obtains an access token.
- Spring Cloud Azure uses this token to request secrets from Azure Key Vault.

- It calls

```text
GET Secret
```

- Azure Key Vault returns secrets:

```text
acos-api-key        → actual API key
acos-application-id → actual application ID
acos-url            → actual external API URL
```

##### 🟦 Step 7: Spring loads secrets into Environment

- **Spring Cloud Azure** loads the **Key Vault** secrets into Spring's **Environment**.
- Environment stores the secret names and their values so Spring can access them.
- So internally `Environment` like:

```text
Spring Environment

acos-api-key       = actual API key
acos-application-id = actual application ID
acos-url           = actual external API URL
```

- Now every Spring component can access them.

##### 🟦 Step 8: ConfigurationProperties binding

- Spring sees **@ConfigurationProperties(prefix = "acos")** and binds `acos.api-key`, `acos.application-id` and `acos.url` to the corresponding fields of **AcosApiProperties**.
- Our **Service** then accesses the values using ordinary Java getters.

```java
@ConfigurationProperties(prefix = "acos")
public class AcosApiProperties {

    private String apiKey;
    private String applicationId;
    private String url;
    // getter
    // setter
}
```

- It looks inside the Environment.

```text
acos.api-key
acos.application-id
acos.url
```

- And Matches them

```text
private String apiKey;
private String applicationId;
private String url;
```

- Creates `AcosApiProperties`
- and stores it as a Spring Bean.

##### 🟦 Step 9: config/RestTemplateConfig.java

```java
@Configuration
public class RestTemplateConfig {

    @Bean
    public RestTemplate restTemplate() {
        return new RestTemplate();
    }
}
```

##### 🟦 Step 10: ClaimsService.java

- Handles the business logic and delegates the external API call to AcosClient

```java
@Service
public class ClaimsService {

    private final ClaimRepository claimRepository;
    private final AcosClient acosClient;

    public ClaimsService( ClaimRepository claimRepository, AcosClient acosClient) {
        this.claimRepository = claimRepository;
        this.acosClient = acosClient;
    }

    public AcosResponse fetchAcosValues(String claimId) {

        ClaimEntity claim = claimRepository.findByClaimId(claimId)
                            .orElseThrow(() -> new EntityNotFoundException( "Claim not found: " + claimId));

        AcosRequest request = new AcosRequest();
        request.setClaimId(claim.getClaimId());
        request.setCustomerId(claim.getCustomerId());

        return acosClient.fetchAcosValues(request);
    }
}
```

##### 🟦 Step 11: AcosClient.java

```java
@Component
public class AcosClient {

    private final RestTemplate restTemplate;
    private final AcosApiProperties properties;

    public AcosClient( RestTemplate restTemplate, AcosApiProperties properties) {
        this.restTemplate = restTemplate;
        this.properties = properties;
    }

    public AcosResponse fetchAcosValues(AcosRequest request) {

        HttpHeaders headers = new HttpHeaders();
        headers.set("x-api-key", properties.getApiKey());
        headers.set("application-id", properties.getApplicationId());
        headers.setContentType(MediaType.APPLICATION_JSON);

        HttpEntity<AcosRequest> entity = new HttpEntity<>(request, headers);

        ResponseEntity<AcosResponse> response = restTemplate.exchange(
                        properties.getUrl(),
                        HttpMethod.POST,
                        entity,
                        AcosResponse.class
                );

        return response.getBody();
    }
}
```

### ➡️ Flow

```text

Spring Boot Starts
        ↓
Reads pom.xml
        ↓
Finds Azure Key Vault Starter
        ↓
AutoConfiguration starts
        ↓
Reads Key Vault endpoint
        ↓
Authenticates (Managed Identity / Service Principal)
        ↓
Azure AD returns Access Token
        ↓
Calls Azure Key Vault
        ↓
Downloads Secrets
        ↓
Loads secrets into Spring Environment
        ↓
@ConfigurationProperties binds Environment values
        ↓
Creates PaymentProperties Bean
        ↓
Injects into PaymentService
        ↓
paymentProperties.getApiKey()
        ↓
Call External API


```

##### Three different layers: 🔴

- **Azure Key Vault** → stores the secrets.
- **Spring Environment** → acts as a central store of all configuration (properties, environment variables, Key Vault secrets, Kubernetes secrets, etc.).
- **@ConfigurationProperties** → maps those configuration values into a strongly typed Java object that your services use.
