🔵🟢🔴➡️⭕🟠🟦🟣🟥🟧✔️ ☑️ • ‣ → ⁕ ⏺️

# ⏺️ Spring Boot annotations

- **1. @Component:** Marks a class as a Spring bean (component). Used for classes that are automatically detected and instantiated by Spring's classpath scanning.

- **2. @Service:** A specialization of @Component, typically used to define service classes (business logic layer).

- **3. @Repository:** A specialization of @Component, used to define Data Access Object (DAO) classes (persistence layer).

- **4. @Controller:** Marks a class as a Spring MVC controller, used to handle HTTP requests.

- **5. @RestController:** A convenience annotation for @Controller and @ResponseBody. It is used to create RESTful web services.

- **6. @Configuration:** Marks a class as a source of bean definitions (Java-based configuration).

- **7. @Bean:** Used inside a @Configuration class to define beans that should be registered in the Spring container.

- **8. @Autowired:** Automatically wires dependencies into Spring beans by type.

- **9. @Inject:** Equivalent to @Autowired, but part of the Java standard (JSR-330).

- **10. @Qualifier:** Used alongside @Autowired to specify which bean to inject when multiple candidates are available.

- **11. @Value:** Used to inject values from property files environment variables into fields methods orconstructor parameters.

- **12. @SpringBootApplication:** A convenience annotation that combines @Configuration, @EnableAutoConfiguration, and @ComponentScan. It is used on the main class to bootstrap a Spring Boot application.

- **13. @EnableAutoConfiguration:** Instructs Spring Boot to automatically configure your application based on the included dependencies.

- **14. @SpringBootTest:** Used for integration testing. It loads the full Spring application context for tests. 15. @EnableScheduling: Enables Spring's scheduledtask execution capability (e.g., cron jobs).

- **15. @EnableAsync:** Enables Spring's asynchronous method execution capability using @Async in the code.

- **16. @RequestMapping:** Used to map HTTP requests to handler methods of MVC and REST controllers.

- **17. @GetMapping:** Shorthand for

@RequestMapping(method = RequestMethod.GET).

- **19. @PostMapping:** Shorthand for

@RequestMapping(method = RequestMethod.POST) 20. @PutMapping: Shorthand for

@RequestMapping(method = RequestMethod.PUT).

- **21. @DeleteMapping:** Shorthand for

@RequestMapping(method =

RequestMethod.DELETE).

- **22. @PatchMappingShorthand** for @RequestMapping(methodRequestMethod.PATCH).

- **23. @RequestParam:** Binds request parameters to method parameters in a controller.

- **24. @PathVariable:** Binds path variables (from URL) to method parameters in a controller.

- **25. @RequestBody:** Binds the HTTP request body to a method parameter in a controller (usually for JSON payload).

- **26. @ResponseBody:** Indicates that the return value of the method should be written directly to the HTTP response body (used in REST controllers). 27. @ModelAttribute: Used to bind request parameters to a model object, often used in forms
