⏺️ ➡️ 🟦 🟩 🟢 🔵 🔷 🔹🔴 ☑️ ✔️ ✓→•←⁕⁂※⁜‣

# ⏺️ Filter vs Interceptors

- Before DispatcherServlet → Filters
- After DispatcherServlet → Interceptors

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

### ➡️ Think of a Shopping Mall

- Filter = Security at the Main Gate
- Interceptor = Security Outside a Specific Shop
