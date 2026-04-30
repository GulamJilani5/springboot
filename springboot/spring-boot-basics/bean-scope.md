⏺️ ➡️ 🟦 🔵 🟢🔴⭕🟠🟣🟥🟧✔️ ☑️ • ‣ → ⁕

# ⏺️ Spring Bean Scope

### ➡️ Singleton

- Find `https://github.com/GulamJilani5/system-design/blob/main/systemdesign/designpattern/creational/singleton.md`
- One instance per Spring container (shared across entire application)

```java
@Service
public class PaymentService {}
```

- In Spring boot Controller, Service & Repository (By Default Singleton — no need to configure anything)

##### 🟦 Where to use

- Business logic services (**PaymentService**, **UserService**)
- Database connection / configuration
- Utility or helper classes

### ➡️ Prototype

- New instance every time bean is requested

```java
@Component
@Scope("prototype")
public class RequestBuilder {}
```

- Use inside service:

```java
@Autowired
private ObjectProvider<RequestBuilder> provider;

RequestBuilder obj = provider.getObject(); // new object
```

##### 🟦 Where to use

- Request/response builders
- Temporary processing objects
- Report/file generation logic

### ➡️ Request

- One instance per HTTP request

```java
@Component
@Scope(value = WebApplicationContext.SCOPE_REQUEST)
public class UserContext {}
```

##### 🟦 Where to use

- Logged-in user info (userId, token)
- Request metadata (headers, traceId)
- Multi-tenant data (tenantId per request)

### ➡️ Session

- One instance per user session

```java
@Component
@Scope(value = WebApplicationContext.SCOPE_SESSION)
public class Cart {}
```

##### 🟦 Where to use

- Shopping cart
- User preferences/settings
- Multi-step form data
