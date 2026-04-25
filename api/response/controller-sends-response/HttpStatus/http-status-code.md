⏺️ ➡️ 🟦 🟩 🟢 🔵 🔷 🔹🔴 ☑️ ✔️ ✓→•←⁕⁂※⁜‣

# ⏺️ 5 Classes of HTTP Status Codes

- **Purpose:**
  - Status codes inform the client about the result of the request, guiding how to proceed (e.g., **retry**, **redirect**, or **display an error**).
- **Custom Status Codes:** Rare, but some APIs define custom codes for specific use cases (not standard).
- Three-digit number in the HTTP response that indicates the outcome of the request.
  - **First digit** → Class (category) of the response
  - **Next two digits** → Specific meaning within that class

### ➡️ 1xx (Informational)

- Request received, processing continues (**e.g:**, 100 Continue).

### ➡️ 2xx (Success): Request successfully processed.

- **200** `OK:`
  - Request succeeded (e.g., for GET or PUT).
- **201** `Created:`
  - Resource created (e.g., for POST).
- **204** `No Content:`
  - Request succeeded, no body returned (e.g., for DELETE).

### ➡️ 3xx (Redirection): Further action needed.

- **301** `Moved Permanently:`
  - Resource moved to a new URL.
- **302** `Found:`
  - Temporary redirect.
- **304** `Not Modified:`
  - Resource unchanged (used for caching).

### ➡️ 4xx (Client Error): Client-side issue.

- **400** `Bad Request:`
  - Malformed request (e.g., invalid JSON).
- **401** `Unauthorized:`
  - Authentication required or failed.
- **403** `Forbidden:`
  - Client lacks permission.
- **404** `Not Found:`
  - Resource doesn’t exist.
- **429** `Too Many Requests:`
  - Rate limit exceeded.

### ➡️ 5xx (Server Error): Server-side issue.

- **500** `Internal Server Error:`
  - Generic server error.
- **502** `Bad Gateway:`
  - Invalid response from upstream server.
- **503** `Service Unavailable:`
  - Server temporarily unavailable.
