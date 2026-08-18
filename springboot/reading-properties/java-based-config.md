⏺️ ➡️ 🟦 🟩 🟢 🔵 🔷 🔹🔴 ☑️ ✔️ ✓→•←⁕⁂※⁜‣

# ⏺️ Reading Proerties Values in Spring Boot With Java Based Configuration

- Suppose application needs these values:
  - URL
  - username
  - application ID

### ➡️ application.properties

- A file where we keep values that may change between environments
  - DEV, QA and PROD.

```java
app.url=https://example.com
app.username=admin
app.application-id=my-app-123
```

### ➡️ Configuration Object

- Instead of reading these properties directly everywhere, we can create a Java class that represents these properties.
- **Configuration object** = Java object that holds configuration values from `application.properties`.

```java
@ConfigurationProperties(prefix = "app")
public class AppProperties {

    private String url;
    private String username;
    private String applicationId;

    // getters and setters
}
```

- Now Spring maps:

```text
app.url            → url
app.username       → username
app.application-id → applicationId
```

### ➡️ Registering the configuration object

- There are two common approaches you were asking about earlier:

##### 🟦 @EnableConfigurationProperties

- Used in a @Configuration class:

```java
@Configuration
@EnableConfigurationProperties(AppProperties.class)
public class AppConfig {
}
```

- If multiple Configuration Objects are present then place them in the array of the **@EnableConfigurationProperties**

```java
@EnableConfigurationProperties(AppProperties.class, AnotherObject.class, AnotherObject.class)
```

##### 🟦 @ConfigurationPropertiesScan

- Placed on the main application class
- Spring, scan the project and automatically find my @ConfigurationProperties classes

```java
@SpringBootApplication
@ConfigurationPropertiesScan
public class MyApplication {
}
```

### ➡️ Use those properties values in the Service or any other class.

```java
@Service
public class UserService {

    private final AppProperties appProperties;

    public UserService(AppProperties appProperties) {
        this.appProperties = appProperties;
    }

    public void test() {

        System.out.println(appProperties.getUrl());
        System.out.println(appProperties.getUsername());
        System.out.println(appProperties.getApplicationId());
    }
}
```
