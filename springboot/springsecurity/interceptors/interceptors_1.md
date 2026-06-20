⏺️ ➡️ 🟦 🟩 🟢 🔵 🔷 🔹🔴 ☑️ ✔️ ✓→•←⁕⁂※⁜‣

# ⏺️ Interceptor

- Think of an interceptor as a security guard standing at the entrance of your application.
- Every HTTP request passes through the guard before reaching the controller.
- Interceptor gets two chances:
  - Before controller execution
  - After controller execution

### ➡️ What Interceptor Does

- Check API Key
- Check JWT Token (if you're not using Spring Security, or perform additional application-level validation)
- Log requests/responses
- Calculate request execution time
- Check user roles/permissions (authorization)
- Audit user activity
- Perform pre-processing and post-processing around controller execution

### ➡️ HandlerInterceptor

- **HandlerInterceptor** interface has `preHandle()`, `postHandle()` & `afterCompletion()` methods.

```java
@Component
public class MyInterceptor implements HandlerInterceptor {

    @Override
    public boolean preHandle(...) {

    }

    @Override
    public void postHandle(...) {

    }

    @Override
    public void afterCompletion(...) {

    }
}
```

### ➡️ Flow

```text
Browser

      |
      v

DispatcherServlet

      |
      v

Interceptor: preHandle()

      |

Controller

      |

Service

      |

Repository

      |

Back to Controller

      |

Interceptor: postHandle()

      |

Response Sent

      |

Interceptor: afterCompletion()

      |

Client
```

##### 🟦 preHandle()

- Runs before controller.

##### 🟦 postHandle()

- Runs after controller.

##### 🟦 afterCompletion()

- Runs after response is completely sent.

### ➡️ But who calls the interceptor / How to configure the Interceptors - WebMvcConfigurer

- Spring does not scan every interceptor automatically but we have to register it.
- Create configuration and override `addInterceptors()`.
- `registry.addInterceptor(interceptor)` means "Dear Spring, for every request, execute MyInterceptor."

```java
@Configuration
public class WebConfig implements WebMvcConfigurer {

    @Autowired
    MyInterceptor interceptor;

    @Override
    public void addInterceptors(InterceptorRegistry registry) {

        registry.addInterceptor(interceptor);
    }

}

```
