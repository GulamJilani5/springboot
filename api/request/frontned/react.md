🟩🟢🔷🔹🔵🟦⏺️➡️

# ⏺️ API Calls from the Frontend (React)

In a React application, API calls are typically made using libraries like fetch (native JavaScript) or axios (a popular third-party library). The components of an API call include headers, body, credentials, and additional configurations.

### ➡️ 1. Headers

Headers are used to provide metadata, authentication, or content specifications.

##### 🟩 What Can Be Sent:

- **Content-Type:** Specifies the body format (e.g., application/json for JSON, multipart/form-data for file  
   uploads).
- **Authorization:** Sends tokens (e.g., Bearer <JWT> for authentication).
- **Accept:** Specifies expected response format (e.g., application/json).
- **Custom Headers:** For API-specific requirements (e.g., X-API-Key).
- **Cache-Control:** Controls caching (e.g., no-store).
- **Origin:** Automatically set by browsers for CORS.

- **🔷Using fetch:**

```js
javascriptfetch("https://api.example.com/users", {
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

- **🔷Using axios:**

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
    }
  )
  .then((response) => console.log(response.data));
```

##### 🟩 Constraints:

- Browsers restrict certain headers (e.g., Host, Referer) for security.
- CORS policies may block custom headers unless allowed by the server (Access-Control-Allow-Headers).

### ➡️ 2. Body

The body contains the data sent to the server, typically for POST, PUT, or PATCH requests.

##### 🟩 What Can Be Sent:

- **JSON:** Most common for REST APIs.
- **Form Data:** For forms or file uploads.
- **Text:** For plain text payloads.
- **Binary Data:** For files (e.g., images, PDFs).

##### 🟩 How to Send in React:

- **🟢JSON:**

- **🔷Using fetch:**

```js
fetch("https://api.example.com/users", {
  method: "POST",
  headers: { "Content-Type": "application/json" },
  body: JSON.stringify({ username: "john_doe", email: "john@example.com" }),
});
```

- **🔷Using axios:**

- **🟢Form Data (e.g., for file uploads):**

- **🔷Using fetch:**

```js
const formData = new FormData();
formData.append("username", "john_doe");
formData.append("file", fileInput.files[0]);

fetch("https://api.example.com/upload", {
  method: "POST",
  body: formData,
});
```

- **🔷Using axios:**

```js
javascriptaxios.post(
  "https://api.example.com/users",
  {
    username: "john_doe",
    email: "john@example.com",
  },
  {
    headers: { "Content-Type": "application/json" },
  }
);
```

##### 🟩 Constraints:

- **GET** and **HEAD** requests typically don’t include a body.
- Ensure the Content-Type header matches the body format.
- Large payloads may require chunked transfer encoding or multipart uploads.

### ➡️ 3. Credentials

Credentials handle authentication and session management, often involving cookies or tokens.

##### 🟩 What Can Be Sent:

- **Cookies:** Automatically sent if credentials is set to include (for session-based authentication).
- **Tokens:** Sent via the Authorization header (e.g., JWT, OAuth tokens).
- **API Keys:** Sent via headers (e.g., X-API-Key) or query parameters.

##### 🟩 How to Send in React:

- 🟢 **With Cookies** (for `session-based` auth):

- **🔷Fetch**

```js
fetch("https://api.example.com/protected", {
  method: "GET",
  credentials: "include", // Sends cookies
});
```

- **🔷Using axios:**

- 🟢**With JWT token:**

- **🔷Fetch**

```js
fetch("https://api.example.com/protected", {
  method: "GET",
  headers: {
    Authorization: `Bearer ${token}`,
  },
});
```

- **🔷Using axios:**

```js
javascriptaxios.get("https://api.example.com/protected", {
  headers: { Authorization: `Bearer ${token}` },
  withCredentials: true, // Sends cookies
});
```

##### 🟩 Constraints:

- **CORS restricts credentials:** 'include' unless the server allows it (Access-Control-Allow-Credentials: true).
- **Tokens** must be securely stored (e.g., in **localStorage** or **HttpOnly** cookies) to prevent XSS attacks.
- API keys in query parameters are less secure due to visibility in URLs.

### ➡️ 4. Additional Configurations

Beyond headers, body, and credentials, other configurations can be sent:

##### 🟩 Query Parameters:

Appended to the URL (e.g., `?id=123&sort=asc`).

- 🔷 **Fetch**

```ja
fetch('https://api.example.com/users?id=123', { method: 'GET' });
```

- 🔷 **Axios**

##### 🟩 Timeout:

Set a request timeout to handle slow servers.

- 🔷 **Fetch**

```js
const controller = new AbortController();
setTimeout(() => controller.abort(), 5000);

fetch("https://api.example.com/users", {
  signal: controller.signal,
});
```

- 🔷 **Axios**

##### 🟩 Mode:

Controls CORS behavior (cors, no-cors, same-origin).

- 🔷 **Fetch**

```js
fetch("https://api.example.com/users", { mode: "cors" });
```

- 🔷 **Axios**

##### 🟩 Cache:

Controls caching behavior (e.g., no-store, reload).

- 🔷 **Fetch**

```js
fetch("https://api.example.com/users", { cache: "no-store" });
```

- 🔷 **Axios**

##### 🟩 Redirect:

Handles redirects (follow, error, manual).

- 🔷 **Fetch**

```js
fetch("https://api.example.com/redirect", { redirect: "follow" });
```

- 🔷 **Axios**
