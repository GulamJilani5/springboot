🔵🟢🔴➡️⭕🟠🟦🟣🟥🟧✔️⏺️ ☑️ • ‣ → ⁕

# ⏺️ DispatcherServlet

- Find Dispatcher serverlet concepts in springboot `D:\Jilani\learning\spring boot\springbootcore\springboot-lifecycle.md`
  **DispatcherServlet** is the front controller in the Spring MVC framework.
  It receives all incoming HTTP requests, then dispatches them to the appropriate controller for further processing.

## Detailed Explanation

- In a Spring MVC application, the DispatcherServlet acts as the central entry point for every web request.
- It sits between the client and the backend business logic.

- **When a request comes in:**

  - Receives the request (front controller pattern).
  - Consults Handler Mappings to find the right controller and method.
  - Calls the controller, which returns a ModelAndView or response.
  - Passes the result to the View Resolver, which decides which view (like a JSP, Thymeleaf template, JSON, etc.) to render.
  - Sends the final response back to the client.

```java
Client → DispatcherServlet → HandlerMapping → Controller
         → ModelAndView → ViewResolver → View → Response

```

- **Key Points to Emphasize in an Interview**

- It follows the Front Controller design pattern.
- Configured automatically when you use @EnableWebMvc or Spring Boot’s auto-configuration.
- It delegates responsibilities rather than handling business logic itself.
- Plays a key role in integrating different components of Spring MVC (`controllers`, `views`, `exception handling`, etc.).
