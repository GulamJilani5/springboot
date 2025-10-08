🔵🟢🔴➡️⭕🟠🟦🟣🟥🟧✔️⏺️ ☑️ • ‣ → ⁕

# ⏺️ Spring Boot Application Lifecycle - From Start to Finish

Ever wondered what happens when you start & stop a Spring Boot application?

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
