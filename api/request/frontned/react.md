⏺️ ➡️ 🟦 🟩 🟢 🔵 🔷 🔹🔴 ☑️ ✔️ ✓→•←⁕⁂※⁜‣

# ⏺️ API Calls from the Frontend (React)

- In React API calls fetch (native JavaScript) or axios (a popular third-party library).
- The components of an API call include URL, HTTP Method, headers, body, credentials, Cookies and additional configurations.

### ➡️ URL

- `https://api.example.com/users?role=admin&page=2`
- Protocol → http / https
- Host → api.example.com
- Path → /users
- Query Params → ?role=admin&page=2

### ➡️ HTTP Method

| Method  | Purpose               |
| ------- | --------------------- |
| GET     | Fetch data            |
| POST    | Create                |
| PUT     | Full update           |
| PATCH   | Partial update        |
| DELETE  | Remove                |
| OPTIONS | Check allowed methods |
| HEAD    | Only headers          |

### ➡️ Headers

Headers are used to provide metadata, authentication, or content specifications.

##### 🟦 Content-Type

- Specifies the body format.
- Multipart/form-data for file uploads.

```text
Content-Type: application/json, multipart/form-data
Content-Length
```

##### 🟦 Authorization

- Sends tokens for authentication.

```text
Authorization: Bearer / Basic / API Key.
```

##### 🟦 Client Info

- User-Agent
- Referer
- Origin

##### 🟦 Accept

- Specifies expected response format (e.g., application/json).

##### 🟦 Custom Headers

- For API-specific requirements

```text
X-Request-ID: 123
X-Tenant-ID: abc
X-API-Key
```

##### 🟦 Caching

- Cache-Control
- If-Modified-Since
- ETag

##### 🟦 Origin

- Automatically set by browsers for CORS.

### ➡️ Body

The body contains the data sent to the server, typically for POST, PUT, or PATCH requests.

##### 🟦 JSON

- Most common for REST APIs.

##### 🟦 Form Data

- For forms or file uploads.

```js
const formData = new FormData();
formData.append("username", "john_doe");
formData.append("file", fileInput.files[0]);

fetch("https://api.example.com/upload", {
  method: "POST",
  body: formData,
});
```

##### 🟦 Text

- For plain text payloads.

##### 🟦 Binary Data

- For files (e.g., images, PDFs).

##### 🟦 Constraints:

- **GET** and **HEAD** requests typically don’t include a body.
- Ensure the Content-Type header matches the body format.
- Large payloads may require chunked transfer encoding or multipart uploads.

### ➡️ Credentials

Credentials handle authentication and session management, often involving cookies or tokens.

##### 🟦 Cookies

- Automatically sent if credentials is set to include (for session-based authentication).

###### 🔵 Fetch

```js
fetch("https://api.example.com/protected", {
  method: "GET",
  credentials: "include", // Sends cookies
});
```

###### 🔵 Using axios

```js
javascriptaxios.get("https://api.example.com/protected", {
  headers: { Authorization: `Bearer ${token}` },
  withCredentials: true, // Sends cookies
});
```

##### 🟦 Tokens

- Sent via **header** (e.g., JWT, OAuth tokens).

##### 🟦 API Keys

- Sent via **header** (e.g., X-API-Key).

##### 🟦 Constraints

- **CORS restricts credentials:** 'include' unless the server allows it (Access-Control-Allow-Credentials: true).
- **Tokens** must be securely stored (e.g., in **localStorage** or **HttpOnly** cookies) to prevent XSS attacks.
- API keys in query parameters are less secure due to visibility in URLs.

### ➡️ Additional Configurations

Beyond headers, body, and credentials, other configurations can be sent:

##### 🟦 Timeout

Set a request timeout to handle slow servers.

```js
const controller = new AbortController();
setTimeout(() => controller.abort(), 5000);

fetch("https://api.example.com/users", {
  signal: controller.signal,
});
```

##### 🟦 Mode

Controls CORS behavior (cors, no-cors, same-origin).

```js
fetch("https://api.example.com/users", { mode: "cors" });
```

##### 🟦 Redirect

Handles redirects (follow, error, manual).

```js
fetch("https://api.example.com/redirect", { redirect: "follow" });
```

##### 🟦 Constraints

- Browsers restrict certain headers (e.g., Host, Referer) for security.
- CORS policies may block custom headers unless allowed by the server (Access-Control-Allow-Headers).

### ➡️ Example

##### 🟦 Using fetch

```js
fetch("https://api.example.com/users", {
  method: "POST",
  headers: {
    "Content-Type": "application/json",
    Authorization: `Bearer ${token}`,
    "X-Custom-Header": "value",
  },
  body: JSON.stringify({ username: "john_doe" }),
})
  .then((response) => response.json())
  .then((data) => console.log(data));
```

##### 🟦 Using axios

```js
import axios from "axios";

axios
  .post(
    "https://api.example.com/users",
    { username: "john_doe" },
    {
      headers: {
        "Content-Type": "application/json",
        Authorization: `Bearer ${token}`,
        "X-Custom-Header": "value",
      },
    },
  )
  .then((response) => console.log(response.data));
```
