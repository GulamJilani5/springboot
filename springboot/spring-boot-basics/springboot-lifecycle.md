🔵🟢🔴➡️⭕🟠🟦🟣🟥🟧✔️⏺️ ☑️ • ‣ → ⁕

# ⏺️ Spring Boot Application Lifecycle - From Start to Finish & Flow

## ➡️ Spring Boot Flow

```java
main()
 ↓
SpringApplication.run()
 ↓
Create SpringApplication instance
 ↓
Read @SpringBootApplication
 - Enables:
   @Configuration
   @ComponentScan
   @EnableAutoConfiguration
 (⚠️ Only enables features, does NOT execute them)
 ↓
Determine Application Type
 (Servlet / Reactive / Non-Web)
 ↓
Prepare Environment
 - Load application.properties / application.yml
 - Load profiles (dev/prod)
 - Load OS env variables
 - Load command-line arguments
 ↓
Create ApplicationContext (EMPTY container)
 ↓
Apply Initializers (ApplicationContextInitializer)
 - Modify context before startup
 ↓
🔴 Refresh ApplicationContext (CORE EXECUTION PHASE)
   ↓
   1. Load Bean Definitions
      - Component Scanning (@ComponentScan)
      - Read @Configuration classes
   ↓
   2. Auto-Configuration
      - Triggered by @EnableAutoConfiguration
      - Uses classpath + properties
   ↓
   3. BeanFactory Post Processing
      - Modify bean definitions
   ↓
   4. Bean Instantiation
      - Create beans (Singleton by default)
      - Dependency Injection (Constructor/Field/Setter)
   ↓
   5. Bean Post Processing
      - BeanPostProcessor
      - @PostConstruct
      - AOP Proxy creation
 ↓
Start Embedded Server (Tomcat/Jetty/Undertow)
 ↓
Publish Events (Listeners execute)
 - ApplicationStartedEvent
 - ApplicationReadyEvent
 ↓
Run CommandLineRunner / ApplicationRunner
 ↓
✅ Application Fully Started (READY TO SERVE REQUESTS)

```

## ➡️ Spring Boot Lifecycle

- Ever wondered what happens when you start & stop a Spring Boot application?

## ➡️ Startup Phase

- **Entry Point** → Application starts from the main() method annotated with @SpringBootApplication.
- **Auto-Configuration**→ Beans are auto-configured based on classpath dependencies (@EnableAutoConfiguration).
- **Component Scanning** → Detects @Component, @Service, @Repository, etc. and registers them in the **ApplicationContext**.
- **Embedded Server** → For web apps, an embedded server (Tomcat/Jetty/Undertow) starts, making it standalone.

## ➡️ Request Processing Phase (Web Applications)

- **Client Request** → A client sends an encrypted request.
- **SSL/TLS Termination** → Embedded server decrypts the request via SSL certificate.
- **DispatcherServlet** → Maps the request to the
- correct controller using HandlerMapping.
- **Controller Processing** → Controller handles logic & interacts with services/repositories.
- **Response Generation** → Response sent back via DispatcherServlet.
- **Client Response** → Server encrypts the response & delivers it securely.

## ➡️ Shutdown Phase

- **Shutdown Trigger** → Application stops (manual kill signal, container stop, etc.).
- **SpringApplication Shutdown Hook** → Gracefully closes the ApplicationContext.
- **Bean Destruction**→ Executes `@PreDestroy` methods & `DisposableBean.destroy()`.
- **Resource Cleanup**→ Closes DB connections, thread pools, schedulers, etc.
- **Graceful Exit** → Publishes `ContextClosedEvent` → JVM exits cleanly.
