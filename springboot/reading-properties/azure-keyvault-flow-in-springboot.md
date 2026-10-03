⏺️ ➡️ 🟦 🔵🔹🔷 🔵 ☑️ ✔️ 🔴 ⭕ • ‣ → ⁕

# ⏺️ How Spring Boot reads the value from the

### ➡️ Azure Key Vault Integration With Spring Boot

##### 🟦 Step 1: Spring Boot starts

```java
@SpringBootApplication
public class Application {

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

- Spring Boot starts creating the ApplicationContext (IoC Container).

##### 🟦 Step 2: Spring reads dependencies

- pom.xml

```text
spring-cloud-azure-starter-keyvault-secrets
```

- Spring Boot sees this dependency. Because of **AutoConfiguration** (`@EnableAutoConfiguration`).
- It says: "Azure Key Vault starter is present. Let me configure Azure Key Vault automatically."
- No code written by developers.

##### 🟦 Step 3: Spring Reads Configuration

- Spring reads from:
- application.properties:

```text
spring.cloud.azure.keyvault.secret.endpoint=...`
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

- Now Spring has the token.
- It calls

```text
GET Secret
```

- Azure Key Vault returns

```text
payment-api-key
payment-token
payment-url
```

##### 🟦 Step 7: Spring loads secrets into Environment

- This is the important step.
- Spring converts `payment-api-key` into `Environment`
- So internally `Environment` like:

```text

payment-api-key = abc123

payment-token = xyz456

payment-url = https://...
```

- Now every Spring component can access them.

##### 🟦 Step 8: ConfigurationProperties binding

```java
@ConfigurationProperties(prefix="payment")
```

- It looks inside the Environment.

```text
Environment

payment.url

payment.api-key

payment.token
```

- And Matches them

```text
private String url;

private String apiKey;

private String token;
```

- Creates `PaymentProperties`
- and stores it as a Spring Bean.

##### 🟦 Step 9: Service Starts & Business Logic

- Now Spring creates `PaymentService` Constructor Injection `PaymentProperties` is injected.
- `paymentProperties.getApiKey();`
- It never talks to Azure.
- It only talks to the already-created bean.

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
