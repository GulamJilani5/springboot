⏺️ ➡️ 🟦 🟩 🟢 🔵 🔷 🔹🔴 ☑️ ✔️ ✓→•←⁕⁂※⁜‣

# ⏺️ WebMvcConfigurer

- **WebMvcConfigurer** is a Spring MVC configuration interface that allows us to customize the default MVC configuration. We typically implement it in a class annotated with `@Configuration` and override only the methods we need, such as `addInterceptors()` to register interceptors or `addCorsMappings()` to configure **CORS**.
- The implementing class can have any name, such as `WebConfig`, `MvcConfig`, or `AppConfig`; the name itself has no special meaning.

### ➡️ WebMvcConfigurer interface Methods

| Method                         | Purpose                                                           |
| ------------------------------ | ----------------------------------------------------------------- |
| `addInterceptors()`            | Register Spring MVC interceptors                                  |
| `addCorsMappings()`            | Configure CORS for your APIs                                      |
| `addResourceHandlers()`        | Serve static resources like images, CSS, JS                       |
| `addViewControllers()`         | Map URLs directly to views without a controller                   |
| `configureViewResolvers()`     | Configure how view names resolve (used in MVC with JSP/Thymeleaf) |
| `addFormatters()`              | Add custom converters and formatters                              |
| `configureMessageConverters()` | Customize request/response converters (e.g., JSON, XML)           |
| `configureAsyncSupport()`      | Configure asynchronous request processing                         |
