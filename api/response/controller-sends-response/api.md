# API Concepts

### Body

- Actual data being sent or received

##### Formats:

- **JSON:** Most common for REST APIs (application/json).
- **XML:** Less common, used in legacy systems (application/xml).
- **Form Data:** For submitting forms (application/x-www-form-urlencoded or multipart/form-data for files).
- **Text:** Plain text (text/plain).
- **Binary:** For files like images or PDFs (application/octet-stream).

##### Constraints:

- Not all HTTP methods support a body (e.g., GET and HEAD typically do not).
- The body is optional in responses, depending on the API design.

### Header

- **Metadata**(additional context or instructions about the request or response)

##### Request Headers:

- **Content-Type:** Specifies the format of the request body (e.g., application/json, multipart/form-data).
- **Authorization:** Carries credentials or tokens (e.g., Bearer <token> for JWT).
- **Accept:** Specifies the response format the client expects (e.g., application/json).
- **User-Agent:** Identifies the client (e.g., browser or application).
- **Cookie:** Sends cookies to the server.

##### Response Headers:

- **Content-Type:** Specifies the format of the response body.
- **Set-Cookie:** Sends cookies to the client.
- **Location:** Used in redirects (e.g., after a POST to indicate the new resource's URL).
- **Cache-Control:** Controls caching behavior (e.g., no-cache, max-age=3600).

##### Custom Headers:

- APIs can define custom headers (e.g., X-API-Key: <key> for API authentication).

##### Constraints:

- Headers are case-insensitive.
- Some headers are restricted by browsers for security (e.g., Host, Origin).
- Large headers can impact performance, so they should be used judiciously.

### Method

- Action to be performed on a resource(Body).

##### HTTP Methods

- **GET:** Retrieve a resource (read-only, no body in request).
- **POST:** Create a new resource (includes a body).
- **PUT:** Update an existing resource or create it if it doesn’t exist (includes a body).
- **PATCH:** Partially update a resource (includes a body with partial data).
- **DELETE:** Remove a resource (usually no body).
- **HEAD:** Retrieve headers only, without a response body.
- **OPTIONS:** Retrieve supported methods for a resource (used for CORS preflight).

##### Idempotency:

- **Idempotent Methods:** `GET`, `PUT`, `DELETE` (repeating the request doesn’t change the outcome after the first
  call).
- **Non-Idempotent:** `POST`, `PATCH` (repeating may create or modify resources multiple times).

##### Safety:

- **Safe Methods:** `GET`, `HEAD`, `OPTIONS` (do not modify resources).
- **Unsafe Methods:** `POST`, `PUT`, `PATCH`, `DELETE` (modify resources).

### Status Code

- Three-digit number in the HTTP response that indicates the outcome of the request.

##### 1xx (Informational):

- Request received, processing continues (e.g., 100 Continue).

- 2xx (Success): Request successfully processed.

200 OK: Request succeeded (e.g., for GET or PUT).
201 Created: Resource created (e.g., for POST).
204 No Content: Request succeeded, no body returned (e.g., for DELETE).

3xx (Redirection): Further action needed.

301 Moved Permanently: Resource moved to a new URL.
302 Found: Temporary redirect.
304 Not Modified: Resource unchanged (used for caching).

4xx (Client Error): Client-side issue.

400 Bad Request: Malformed request (e.g., invalid JSON).
401 Unauthorized: Authentication required or failed.
403 Forbidden: Client lacks permission.
404 Not Found: Resource doesn’t exist.
429 Too Many Requests: Rate limit exceeded.

5xx (Server Error): Server-side issue.

500 Internal Server Error: Generic server error.
502 Bad Gateway: Invalid response from upstream server.
503 Service Unavailable: Server temporarily unavailable.

Purpose: Status codes inform the client about the result of the request, guiding how to proceed (e.g., retry, redirect, or display an error).
Custom Status Codes: Rare, but some APIs define custom codes for specific use cases (not standard).
