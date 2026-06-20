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
