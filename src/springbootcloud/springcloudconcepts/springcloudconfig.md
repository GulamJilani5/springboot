# Spring Cloud

### 1. Spring Cloud Config Server
The Config Server is a **centralized configuration management server**. It acts as a middle layer between 
your configuration source (like `Git` or `Vault`) and your microservices.
**📌 Responsibilities:**
- Reads configuration from a backend (Git, file system, Vault, etc.).
- Exposes configuration through REST APIs.
- Serves the right configuration to the clients based on the application name and profile.
- 
### 2. Spring Cloud Config Client
The Config Client is the **microservice** or **Spring Boot** application that connects to the **Config Server** to load its configuration at startup.
**📌 Responsibilities:**
- Connects to Config Server on startup.
- Fetches its own configuration (based on app name & profile).
- Overrides local application.properties.

### ✅ Ways to Read Configurations from the Spring Cloud Config Server

Spring Cloud Config Server supports multiple backends for loading externalized configuration.
**Below are the three most common methods:**

---

##### 1. 📁 Reading Configurations from the Classpath (Inside the JAR)

- Configuration files are stored inside the **resources folder** of the Config Server application.
- Useful for **simple demos or testing**.

**Setup Example:**
```yaml
spring:
  profiles:
    active: native
  cloud:
    config:
      server:
        native:
          search-locations: classpath:/config
```

**Place config files in:**
`src/main/resources/config/`
**Example file:88
`src/main/resources/config/order-service-dev.yml`

##### 2. 📂 Reading Configurations from the File System
- Config files are external to the application JAR, placed on the host file system.
- Ideal for dev/test/production environments where you don’t want to repackage the JAR to update config.
**Setup Example:**
```yml
  spring:
   profiles:
    active: native
   cloud:
    config:
     server:
      native:
       search-locations: file:///opt/configs/
```
**Place your files like:**
`/opt/configs/order-service-dev.yml`

##### 3. 🔁 Reading Configurations from a Git Repository
- Most widely used in real-world microservices.
- Enables version-controlled, centralized, and remote configuration.
- Supports GitHub, GitLab, Bitbucket, or internal Git.


**Setup Example:**
```yml
spring:
  cloud:
    config:
      server:
        git:
          uri: https://github.com/my-org/config-repo
          default-label: main
          search-paths: config

```

**Config repo file structure:**
`config/
  order-service-dev.yml
  payment-service-prod.yml
`

## 🔄 How They Work Together
+----------------------+       +------------------------+
|  Config Client       | <-->  | Spring Cloud Config    |
|  (order-service)     |       | Server                 |
|                      |       | (reads from Git)       |
+----------------------+       +------------------------+
                                          |
                                          v
                           +-----------------------------------+
                           |  Git Repo or File System or Vault |
                           +-----------------------------------+


