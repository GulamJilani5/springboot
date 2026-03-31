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
