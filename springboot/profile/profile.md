⏺️ ➡️ 🟦 🔵 🟢🔴⭕🟠🟣🟥🟧✔️☑️ • ‣ → ⁕

# ⏺️ Profile

- If no profile is active then spring uses `default` profile, No `spring.profiles.active` and nothing is configured.
- Profile logical name used to group bean definitions and configurations so that different environments can run different configurations without changing code.
- Profiles allow one codebase to behave differently in DEV, TEST, QA, PROD, etc.

| Area     | Dev              | Test        | Prod          |
| -------- | ---------------- | ----------- | ------------- |
| Database | H2 / local MySQL | Test DB     | Production DB |
| Logging  | DEBUG            | INFO        | WARN / ERROR  |
| URLs     | localhost        | test-domain | prod-domain   |
| Security | relaxed          | partial     | strict        |

- With profiles environment-based auto-selection.

### 🟦 Profile Name

- A string identifier like:

`dev`
`test`
`qa`
`prod`
`local`

### 🟦 Standard files

```java
application.properties
application.yml

```

### 🟦 Profile-specific files

```java
application-dev.properties
application-test.properties
application-prod.properties

```

- **Spring loads:**
  `application.properties  +  application-{active-profile}.properties`

## ➡️ Ways to Activate Profiles

- Priority order (highest → lowest):

```java
Command line
JVM argument
Environment variable
application.properties

```

### 🟦 application.properties

```java
spring.profiles.active=dev

```

### 🟦 application.yml

```java
spring:
  profiles:
    active: prod

```

### 🟦 JVM Argument (Most Used in PROD)

```java
-Dspring.profiles.active=prod

```

### 🟦 Command Line

```java
java -jar app.jar --spring.profiles.active=test

```

### 🟦 Environment Variable (Docker / Kubernetes)

```java
SPRING_PROFILES_ACTIVE=prod

```

## ➡️ @Profile Annotation (Bean-Level Control)

- You can enable/disable beans based on profile.
- Only one bean loads based on active profile.

```java
@Configuration
public class AppConfig {

    @Bean
    @Profile("dev")
    public DataSource devDataSource() {
        return new HikariDataSource();
    }

    @Bean
    @Profile("prod")
    public DataSource prodDataSource() {
        return new HikariDataSource();
    }
}

```

## ➡️ @Profile at Class Level

```java
@Profile("test")
@Service
public class TestEmailService implements EmailService {
}

```

```java
@Profile("prod")
@Service
public class SmtpEmailService implements EmailService {
}

```

## ➡️ Multiple Profiles Together

- You can activate multiple profiles at once:

```java
spring.profiles.active=dev,debug

```

- Then use:

```java
@Profile({"dev","debug"})

```

## ➡️ Profile Groups (Advanced & Powerful)

- Used when one profile should activate many others.
- application.yml

```java
spring:
  profiles:
    group:
      prod:
        - db-prod
        - security-prod
        - logging-prod

```

- Now activating:

```java
spring.profiles.active=prod

```

- Automatically activates:

```java
db-prod, security-prod, logging-prod

```

## ➡️ Profiles & Security (Real World)

- Typical setup:

```java
dev     → relaxed CORS, no auth
test    → token auth
prod    → OAuth2 / JWT / strict CORS

```

- Using:

```java
@Profile("prod")
@Configuration
@EnableWebSecurity
public class ProdSecurityConfig {
}

```

## ➡️ Profiles with Docker & Kubernetes( Industry standard )

- Docker

```java
ENV SPRING_PROFILES_ACTIVE=prod

```

- Kubernetes

```java
env:
  - name: SPRING_PROFILES_ACTIVE
    value: prod

```

## ➡️ @ActiveProfiles (Testing)

- Used ONLY in tests
- Ensures test configs are used
- Prevents prod DB usage accidentally

```java
@SpringBootTest
@ActiveProfiles("test")
class UserServiceTest {
}

```

```java

```

```java

```
