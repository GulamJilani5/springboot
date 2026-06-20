⏺️ ➡️ 🟦 🟩 🟢 🔵 🔷 🔹🔴 ☑️ ✔️ ✓→•←⁕⁂※⁜‣

# ⏺️ Filters

- A **Filter** is actually part of the **Servlet API**, not **Spring**.
- Every HTTP request passes through the Filter before reaching your controller, and every HTTP response passes through the Filter before going back to the client.
- **Filter** is a Interface - `jakarta.servlet.Filter`

### ➡️ What Filter Does

- Validate JWT/API Key (commonly done here when using Spring Security)
- CORS handling
- Set request/response character encoding (UTF-8)
- Compress requests/responses (e.g., GZIP)
- Log all incoming requests (including those that never reach Spring MVC)
- Add or modify HTTP headers
- Wrap HttpServletRequest/HttpServletResponse
- Block malicious or invalid requests before they reach Spring

### ➡️ Flow

```text
                      HTTP REQUEST
                            │
                            ▼
                  Embedded Tomcat Server
                            │
                            ▼
         ┌───────────────────────────────────┐
         │          FILTER 1                 │
         │   (Logging / CORS / Encoding)     │
         └───────────────────────────────────┘
                            │
                            ▼
         ┌───────────────────────────────────┐
         │          FILTER 2                 │
         │ (JWT/API Key/Compression etc.)    │
         └───────────────────────────────────┘
                            │
                            ▼
                 DispatcherServlet
              (Front Controller of Spring)
                            │
                            ▼
         ┌───────────────────────────────────┐
         │ Interceptor.preHandle()           │
         └───────────────────────────────────┘
                            │
                            ▼
                      Controller
                            │
                            ▼
                        Service
                            │
                            ▼
                      Repository
                            │
                            ▼
                        Database
                            │
                (Business Logic Ends)
                            │
                            ▲
                      Repository
                            ▲
                        Service
                            ▲
                      Controller
                            ▲
         ┌───────────────────────────────────┐
         │ Interceptor.postHandle()          │
         └───────────────────────────────────┘
                            ▲
         ┌───────────────────────────────────┐
         │ Interceptor.afterCompletion()     │
         └───────────────────────────────────┘
                            ▲
                  DispatcherServlet
                            ▲
         ┌───────────────────────────────────┐
         │          FILTER 2                 │
         └───────────────────────────────────┘
                            ▲
         ┌───────────────────────────────────┐
         │          FILTER 1                 │
         └───────────────────────────────────┘
                            ▲
                      HTTP RESPONSE
```

### ➡️ Why were Filters introduced?

- Imagine thousands of API endpoints.

```text
/login
/register
/profile
/payment
/orders
/products
/admin
..
..
```

- Suppose every API needs:
  - Logging
  - Authentication
  - Compression
  - CORS
  - Encoding
- Without **Filters**, every controller would repeat the same code for all the endpoints
  - checkToken()
  - logRequest()
  - calculateTime()

- This violates the DRY (Don't Repeat Yourself) principle.

### ➡️ Two ways to register a Filter

- In both approaches, we must have a Filter implementation.

###### 🔵 Think of it like this

- You buy a security guard (your LoggingFilter).
- Now you have two ways to assign him:
  - Automatic assignment (default)
    - "Guard, stand at the main entrance and check everyone."
  - Manual assignment (FilterRegistrationBean)
    - "Guard, stand only at the VIP entrance (/admin/\*) and work before the CCTV guard."
- The guard is the same. Only the assignment changes.

##### 🟦 1. Implement Filter interface

- Spring Boot automatically registers it.
- By default, it applies to all URL patterns (/\*).
- Best when you want the Filter to run for every request.

##### 🟦 2. FilterRegistrationBean

- We still have a class that implements Filter.
- It is only a registration/configuration mechanism.
- It lets you configure:
  - Specific URL patterns (/api/_, /admin/_)
  - Filter order
  - Filter name
  - Enable/disable registration
