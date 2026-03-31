⏺️ ➡️ 🟦 🔵 🟢🔴⭕🟠🟣🟥🟧✔️ ☑️ • ‣ → ⁕

# ⏺️ @Async

- Find More `D:\Jilani\learning\system design\typeofsystems\communicationpattern\asynchronous\asynchronous.md`
- non-blocking async call, **@Async** runs a method in a different thread (TaskExecutor thread pool).
- `CompletableFuture` in Java is very similar to Promises in JavaScript.
- It is spring AOP and only works when called form the another class not within the class.
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

## ➡️ @Async, There Are Only Two Practical Ways To Use It

### 🟦 Scenario 1: @Async with void (Fire-and-forget)

##### 🔵 Enable Async In The Main Method

```java
   @SpringBootApplication
    @EnableAsync
    public class MyApplication {
        public static void main(String[] args) {
            SpringApplication.run(MyApplication.class, args);
        }
    }


```

##### 🔵

```java
  @Service
  public class EmailService {

    @Async
    public void sendEmail(String recipient) {
        try {
            Thread.sleep(3000); // simulating delay
        } catch (InterruptedException e) { }

        System.out.println("Email sent to: " + recipient);
    }
 }

```

##### 🔵 Calling from the Controller

```java
  emailService.sendEmail("john@example.com");

```

### 🟦 Scenario 2: @Async with CompletableFuture<T> (Return result asynchronously)

##### 🔵 Enable Async In The Main Method

```java
   @SpringBootApplication
    @EnableAsync
    public class MyApplication {
        public static void main(String[] args) {
            SpringApplication.run(MyApplication.class, args);
        }
    }


```

##### 🔵

```java
  @Service
public class EmailService {

    @Async
    public CompletableFuture<String> sendEmail(String recipient) {
        try {
            Thread.sleep(3000);
        } catch (InterruptedException e) {}

        String result = "Email sent to: " + recipient;

        return CompletableFuture.completedFuture(result);
    }
}


```

##### 🔵 Calling from the Controller

```java

  @RestController
public class EmailController {

    @Autowired
    private EmailService emailService;

    @GetMapping("/send")
    public String sendEmail() throws Exception {

        CompletableFuture<String> future =
                emailService.sendEmail("john@gmail.com");

        // Do some other work here (non-blocking)

        String result = future.get();   // Wait and get the result

        return result;
    }
}


```

## ➡️ Use Cases

- Sending emails
- Sending notifications
- Background report generation
- Heavy computations
- File processing
- Long API calls
- Data sync tasks
