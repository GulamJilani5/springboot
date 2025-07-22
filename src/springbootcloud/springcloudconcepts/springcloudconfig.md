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

