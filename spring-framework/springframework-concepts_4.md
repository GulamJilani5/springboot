⏺️ ➡️ 🟦 🔵 🟢🔴⭕🟠🟣🟥🟧✔️ ☑️ • ‣ → ⁕

# ⏺️ @Async

- @Async = Proxy + TaskExecutor + Thread Management

- How `@Async` Works Internally in Spring Boot
- Ever wondered how `@Async` works internally in Spring Boot?
- Here’s what happens behind the scenes 👇

## ➡️ Step-by-Step Process

### 🟦 1. **Proxy Creation**

- Spring creates an **AOP proxy** for beans that have methods annotated with `@Async`.
- This happens during bean post-processing (via `AsyncAnnotationBeanPostProcessor`).

### 🟦 2. **Method Interception**

- When a method annotated with `@Async` is called, the **proxy intercepts** the call before the actual method execution.

### 🟦 3. **Task Submission**

- The intercepted method call is **submitted to a `TaskExecutor`**.
- Default executor: `SimpleAsyncTaskExecutor`.
- You can customize it by defining your own `TaskExecutor` bean and enabling async via `@EnableAsync`.

### 🟦 4. **Thread Execution**

- The task runs in a **separate thread**, freeing up the original caller.
- This allows asynchronous (non-blocking) execution.

5. **Return Handling**
   - If the method returns a `Future` or `CompletableFuture`, Spring manages the result **asynchronously**.
   - For `void` return types, the caller simply doesn’t wait for the task to complete.

---

## ⚙️ Important Components

| Component                            | Responsibility                                                      |
| ------------------------------------ | ------------------------------------------------------------------- |
| **TaskExecutor**                     | Executes async tasks (can be customized using `@EnableAsync`).      |
| **AsyncAnnotationBeanPostProcessor** | Scans for `@Async` methods and creates proxies for them.            |
| **AOP Proxy**                        | Intercepts method calls and routes them through the `TaskExecutor`. |

---

````java

---

## ⚡ Configuration Example

```java
@Configuration
@EnableAsync
public class AsyncConfig {

    @Bean(name = "taskExecutor")
    public Executor taskExecutor() {
        ThreadPoolTaskExecutor executor = new ThreadPoolTaskExecutor();
        executor.setCorePoolSize(3);
        executor.setMaxPoolSize(5);
        executor.setQueueCapacity(100);
        executor.setThreadNamePrefix("AsyncThread-");
        executor.initialize();
        return executor;
    }
}

````
