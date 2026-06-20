⏺️ ➡️ 🟦 🟩 🟢 🔵 🔷 🔹🔴 ☑️ ✔️ ✓→•←⁕⁂※⁜‣

# ⏺️ Filters Interface

- It contains three lifecycle methods.
  - init()
  - doFilter()
  - destroy()

### ➡️ init()

- Runs only once.

```text
Application Starts

↓

Filter Created

↓

init()
```

```java
public void init(FilterConfig config) {

    System.out.println("Filter Initialized");

}
```

- Used for:
  - loading configuration
  - reading files
  - opening resources

### ➡️ doFilter()

- This is the heart of the Filter.
- Runs for every request.

```text
GET /users

↓

doFilter()

↓

Controller
```

```java
void doFilter(
        ServletRequest request,
        ServletResponse response,
        FilterChain chain)
```

##### 🟦 ServletRequest

- Incoming request contains
  - headers
  - body
  - parameters
  - cookies
  - remote IP

```java
request.getParameter("id");
```

##### 🟦 ServletResponse

- Outgoing response.
- It can modify headers, modify content type & set status code

```java
response.setContentType("application/json");
```

##### 🟦 FilterChain

- This is the most important object.
- It tells the filter: Continue processing.

```text
Filter

↓

chain.doFilter()

↓

Next Filter

↓

DispatcherServlet

↓

Controller
```

```java
public void doFilter(...) {

    System.out.println("Before the controller");

    chain.doFilter(request,response);

    System.out.println("After the controller");

}
```

- If we don't call `chain.doFilter(request,response);` then request stops and controller will never execute.

### ➡️ destroy()

- Runs when application stops.

```text
Application Shutdown

↓

destroy()
```

```java
public void destroy() {

    System.out.println("Filter Destroyed");

}
```
