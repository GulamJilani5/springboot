⏺️ ➡️ 🟦 🟩 🟢 🔵 🔷 🔹🔴 ☑️ ✔️ ✓→•←⁕⁂※⁜‣

# ⏺️ Status Line

- The Status Line is the very first line of an HTTP response sent from the server to the client.
- This line is mandatory in every HTTP response.
- It tells the client:
  - Which HTTP version is being used
  - What the status code is (e.g., 200, 404, 201)
  - A short human-readable description (called Reason Phrase)

```text
HTTP/1.1 200 OK
HTTP-Version Status-Code Reason-Phrase
```

- **HTTP/1.1** → HTTP version (most common is 1.1, now many use HTTP/2 or HTTP/3)
- **200** → Status Code (the 3-digit number we discussed earlier)
- **OK** → Reason Phrase (short text description of the status code)

### ➡️ Full Structure of an HTTP Response

- Status Line = First line
- Then come Headers
- Then a blank line (\r\n)
- Then the Body (optional)

```text
HTTP/1.1 200 OK                    ← Status Line
Content-Type: application/json     ← Headers start
Content-Length: 123
Set-Cookie: jwt=abc123; HttpOnly
Date: Sun, 26 Apr 2026 01:00:00 GMT

{"id": 45, "name": "Gulam", "status": "success"}   ← Body starts after empty line
```

- Spring Boot (internally) converts your decision into the full HTTP response, including the Status Line.
- Spring uses the embedded server (Tomcat, Jetty, Undertow, Netty, etc.).
- The server is responsible for writing the actual bytes on the wire, including the Status Line.
