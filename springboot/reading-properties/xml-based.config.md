⏺️ ➡️ 🟦 🟩 🟢 🔵 🔷 🔹🔴 ☑️ ✔️ ✓→•←⁕⁂※⁜‣

# ⏺️ Reading Proerties Values in Spring Boot with XML Based Configuration

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
- There is no **@ConfigurationProperties** here as We're going to configure the object through XML

```java

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

### ➡️ Registering the configuration object in applicationContext.xml

```java
<?xml version="1.0" encoding="UTF-8"?>

<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="
           http://www.springframework.org/schema/beans
           https://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="appProperties"
          class="com.example.config.AppProperties">

        <property name="url" value="${app.url}"/>

        <property name="username" value="${app.username}"/>

        <property name="applicationId" value="${app.application-id}"/>

    </bean>

</beans>
```

### ➡️ Use those properties values in the Service or any other class.

```java
package com.example.service;

import com.example.config.AppProperties;
import org.springframework.stereotype.Service;

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
