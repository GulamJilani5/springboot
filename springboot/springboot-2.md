⏺️ ➡️ 🟦 🔵🟢🔴⭕🟠🟣🟥🟧✔️ ☑️ • ‣ → ⁕

# ⏺️ Spring Boot Startup Flow

## ➡️ Create ApplicationContext

```jav
SpringApplication.run(Application.class, args);

```

- Spring Boot decides which type of context to create:
  - **Web app** → ServletWebServerApplicationContext
  - **Reactive app** → ReactiveWebServerApplicationContext
  - **Non-web** → ApplicationContext

## ➡️ Classpath Scanning & Configuration Discovery

- Spring looks for:
  - **@SpringBootApplication** - @Configuration(@SpringBootConfiguration), @ComponentScan, @EnableAutoConfiguration
- Component scanning starts from the base package and Detects Detects:
  - @Component, @Service, @Repository, @Controller, @RestController, @Configuration

## ➡️ Auto-Configuration

- auto-configuration happens before bean creation, but contributes bean definitions
- Which libraries are present in classpath
- Which beans already exist
- Which properties are defined in `application.properties / yml`
- Based on conditions:
  - @ConditionalOnClass, @ConditionalOnBean, @ConditionalOnProperty
- Spring Boot auto-creates beans like:
  - DataSource
  - EntityManager
  - DispatcherServlet
  - Security filters

## ➡️ Bean Lifecycle (Bean Definiton, Creation & Injection)

#### 🟦 Bean Definition Creation

- At this stage:
  - Spring does NOT create objects yet
  - It only creates Bean Definitions (metadata)
- Includes:
  - Class name
  - Scope (singleton, prototype)
  - Dependencies
  - Init / destroy methods

- Think of it as:
  - “Spring now knows WHAT to create”

#### 🟦 Bean Instantiation (Creating Beans)

- Now Spring starts creating beans:
  - Singleton beans are created eagerly
  - Prototype beans are created on demand

#### 🟦 Dependency Injection (Wiring Dependencies)

- Spring resolves:
  - @Autowired
  - Constructor injection
  - Setter injection
  - @Value

- Order:
  - Create bean
  - Inject dependencies
  - Resolve circular dependencies (if possible)

#### 🟦 Bean Lifecycle Callbacks

- Spring invokes:
  - Aware interfaces
  - @PostConstruct
  - InitializingBean
  - Custom init methods

#### 🟦 Application Ready

- Finally:
  - Embedded server starts (Tomcat/Jetty)
  - CommandLineRunner / ApplicationRunner executes
  - App is ready to serve requests
