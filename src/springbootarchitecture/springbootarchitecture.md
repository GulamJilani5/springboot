# ✅ Additional Folders for a Complex Spring Boot Application

| Folder                   | Purpose                                                                   |
| ------------------------ | ------------------------------------------------------------------------- |
| **config/**              | Configuration classes (e.g., security, Swagger, CORS, message converters) |
| **util/** or **common/** | Utility classes or shared logic (e.g., StringUtils, DateUtils)            |
| **mapper/**              | Classes for converting between entities and DTOs (e.g., using MapStruct)  |
| **constants/**           | Central location for application-wide constants                           |
| **enums/**               | Enums used across the application (e.g., `Status`, `Role`)                |
| **security/**            | Security configuration and filters (Spring Security, JWT)                 |
| **scheduler/**           | Scheduled jobs (e.g., for cron-based tasks)                               |
| **listener/**            | Event listeners for application events or domain events                   |
| **interceptor/**         | Custom interceptors for HTTP requests/responses                           |
| **filter/**              | Custom filters for request processing                                     |
| **event/**               | Application events and handlers (if using Spring Events pattern)          |
| **integration/**         | Code that integrates with external systems or APIs                        |
| **test/**                | Unit and integration test cases (under `src/test/java/`)                  |


# ✅Example Project Structure for a Complex App

        src/
        └── main/
        ├── java/
        │   └── com/example/app/
        │       ├── config/
        │       ├── controller/
        │       ├── dto/
        │       ├── exception/
        │       ├── mapper/
        │       ├── model/
        │       ├── repository/
        │       ├── security/
        │       ├── service/
        │       ├── util/
        │       ├── constants/
        │       ├── enums/
        │       └── Application.java
        └── resources/
        ├── application.yml
        ├── static/
        └── templates/


# 🆚 DTO vs Mapper in Spring Boot

In a layered architecture, **DTO** and **Mapper** serve different roles:

---

## 📄 DTO (Data Transfer Object)

**Purpose**:  
A DTO is a plain Java object used to carry data between layers (e.g., from the controller to the service or to the client). It helps to avoid exposing the internal entity structure directly.

**🔑 Key Points**:
- Does **not contain business logic**.
- Used for **input/output in REST APIs**.
- Contains only necessary fields to expose.
- Can include validation annotations like `@NotNull`, `@Size`, etc.

**🧪 Example**:
   
        public class UserDTO {
            private String name;
            private String email;
            // getters and setters
        }

--- 

## 🔄 Mapper

#### Purpose:
  A **Mapper** is a component used to **convert between entities and DTOs** (or between different 
  types of DTOs). This keeps your service and controller code clean and separated from conversion logic.

#### 🔑 Key Points:
- Acts as a **bridge** between your entity and DTO.
- You can write it **manually** or use tools like **MapStruct**, **ModelMapper**, etc.
- Encourages **Separation of Concerns**.

