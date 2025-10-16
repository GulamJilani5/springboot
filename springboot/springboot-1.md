🔵🟢🔴➡️⭕🟠🟦🟣🟥🟧✔️⏺️ ☑️ • ‣ → ⁕

# ⏺️ Spring Boot favors convention-over-configuration, auto-wiring beans via starters.

- `D:\Jilani\learning\spring boot\spring-framework\springframework-concepts_2.md`

## ➡️ Convention Over Configuration

- Normally in Spring (without Spring Boot), you have to manually configure many things:
  - bean definitions, data sources, security settings
  - DataSource (database connection)
  - DispatcherServlet
  - Component scanning packages
  - ViewResolvers
  - Message converters, etc.
- This leads to verbose **XML** files, **Java config classes**, or **annotations scattered** everywhere.
- With Spring Boot, you follow some standard conventions, and Spring Boot automatically configures many things for you.

### 🟦 Without Spring Boot (traditional Spring):

```java
@Configuration
@EnableWebMvc
@ComponentScan(basePackages = "com.example.demo")
public class AppConfig {
    // manually configure beans, view resolvers, data sources etc.
}

```

- You would also need a web.xml or DispatcherServlet configuration.

### 🟦 With Spring Boot

- Just create a class with @SpringBootApplication.
- Put your code in the standard package structure.
- Add a few dependencies (starters), and run.

```java
@SpringBootApplication
public class MyApplication {
    public static void main(String[] args) {
        SpringApplication.run(MyApplication.class, args);
    }
}

```

- No manual XML.
- No explicit bean definitions for common stuff.
- Boot auto-configures things if you follow the conventions (e.g., putting your `@SpringBootApplication` at the root package and keeping components inside sub-packages).

#### 🔵 How It Works in Spring Boot:

- **Auto-Detection:** If you name your main application class` MyAppApplication` and place it in the root package (e.g., `com.example`), Spring Boot automatically scans for components in that package and sub-packages—no need for explicit `@ComponentScan`.
- **Embedded Servers:** By default, it starts an embedded **Tomcat server** on port **8080**. Want Jetty instead? Just exclude Tomcat and include Jetty—no XML server config required.
- **Profiles and Environments:** It auto-loads `application.properties` or `application.yml` for defaults, switching to `application-dev.properties` if you set `spring.profiles.active=dev`.
- **Database Auto-Setup:** If you're using **JPA/Hibernate**, it auto-creates tables from entities (via `spring.jpa.hibernate.ddl-auto=update`) without manual schema scripts.

- **Example**
  - **Traditional Spring:** You'd define a data source bean in XML or Java config, specify transaction managers, and enable **JPA** repositories manually.
  - **Spring Boot:** Add the `spring-boot-starter-data-jpa` dependency, annotate your entity with `@Entity`, and your repository with `@Repository`. Boot auto-configures the **DataSource**, **EntityManagerFactory**, and **TransactionManager**. Just run the app!

#### 🔵 Auto-wiring Beans via Starters

- "Auto-wiring" refers to Spring's dependency injection (DI) mechanism, where beans (managed objects) are automatically connected based on types or names (e.g., via @Autowired).
- Spring Boot uses “starters” — which are pre-packaged dependency bundles that bring in libraries and auto-configuration.
- Starters are curated dependency descriptors (`Maven/Gradle POMs`) that bundle related libraries and their transitive dependencies. Together, they enable **"auto-configuration":** When you include a starter, Boot inspects your classpath and automatically creates and wires the necessary beans—no manual bean definitions needed.

##### → How Starters Enable Auto-Wiring

- **Starters as Entry Points:** Each starter (e.g., spring-boot-starter-web) includes Spring Boot's auto-configuration classes. These use @ConditionalOnClass, @ConditionalOnMissingBean, etc., to check if certain classes are on the classpath and create beans only if needed.
- **Classpath Scanning:** At startup, Boot's AutoConfigurationImportSelector loads relevant @Configuration classes from starters. It then wires beans into your context.

- **Examples of Auto-Wiring**

  - **Web Starter (spring-boot-starter-web):** Includes **Spring MVC**, **Tomcat**, **Jackson**. Auto-wires a **DispatcherServlet**, **WebMvcConfigurer**, and **JSON converters**. Your `@RestController` beans are auto-detected and injected.
  - **Data JPA Starter (spring-boot-starter-data-jpa):** Auto-configures **JpaRepository** implementations, **DataSource** (from `spring.datasource.url`), and **EntityManager**. Inject `@Autowired` private **UserRepository** repo;—it just works.
  - **Security Starter (spring-boot-starter-security):** Auto-wires **SecurityFilterChain** with default HTTP Basic auth. Customize via `@Bean` overrides.

- **pom.xml (Maven)**

```java
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>
```

- **Main Application**

```java
  @SpringBootApplication  // Enables auto-configuration + component scanning
public class MyApp {
    public static void main(String[] args) {
        SpringApplication.run(MyApp.class, args);
    }
}
```

- **Controller (Auto-Wired)**

```java
  @RestController
public class UserController {
    @Autowired  // Auto-wires the auto-configured UserRepository
    private UserRepository userRepository;

    @GetMapping("/users")
    public List<User> getAll() {
        return userRepository.findAll();  // Boot auto-implemented this method
    }
}
```

- **Result:** Run `mvn spring-boot:run`, and you have a full **CRUD API** with database connectivity — no explicit bean wiring!
