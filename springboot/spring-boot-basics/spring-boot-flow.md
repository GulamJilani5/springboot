⏺️ ➡️ 🟦 🔵 🟢🔴⭕🟠🟣🟥🟧✔️ ☑️ • ‣ → ⁕

# ⏺️ Spring Boot Flow

```text
1. SpringApplication.run() is called
   → ApplicationContext is created

2. @SpringBootApplication is processed
   ├─ @SpringBootConfiguration (@Configuration)
   │   → allows @Bean methods
   │
   ├─ @ComponentScan
   │   → scans packages
   │   → registers bean definitions (metadata / recipes)
   │
   └─ @EnableAutoConfiguration
       → loads auto-configuration classes
       → evaluates conditions
       → registers conditional bean definitions

3. ALL bean definitions are now collected
   (from @Configuration, @ComponentScan, @EnableAutoConfiguration)

4. Bean lifecycle starts
   ├─ Instantiate beans (objects created)
   ├─ Inject dependencies (@Autowired / constructor injection)
   ├─ Invoke lifecycle callbacks
       (@PostConstruct, InitializingBean, etc.)

5. Application is READY
   ├─ Embedded server started
   ├─ CommandLineRunner / ApplicationRunner executed
   └─ Application serves requests


```

- `D:\Jilani\learning\spring boot\spring-framework\springframework-concepts_2.md`

## ➡️ Without Spring Boot (traditional Spring):

- Normally in Spring (without Spring Boot), you have to manually configure many things:
  - bean definitions, data sources, security settings
  - DataSource (database connection)
  - DispatcherServlet
  - Component scanning packages
  - ViewResolvers
  - Message converters, etc.
- This leads to verbose **XML** files, **Java config classes**, or **annotations scattered** everywhere.
- With Spring Boot, you follow some standard conventions, and Spring Boot automatically configures many things for you.

```java
@Configuration
@EnableWebMvc
@ComponentScan(basePackages = "com.example.demo")
public class AppConfig {
    // manually configure beans, view resolvers, data sources etc.
}

```

- You would also need a web.xml or DispatcherServlet configuration.

## ➡️ With Spring Boot (Spring Boot Flow)

- SpringConvention Over Configuration, @SpringBootApplication, spring Auto configuration and Spring bean management

- Just create a class with **@SpringBootApplication**.
- Put your code in the standard package structure.
- Add a few dependencies (starters), and run.

```java
@SpringBootApplication //  Spring Boot Configuration  + component scanning + Enables auto-configuration
public class MyApplication {
    public static void main(String[] args) {
        SpringApplication.run(MyApplication.class, args);
    }
}

```

- No manual XML.
- No explicit bean definitions for common stuff.
- Boot auto-configures things if you follow the conventions (e.g., putting your `@SpringBootApplication` at the root package and keeping components inside sub-packages).

### 🟦 1. Creating Context

- When `SpringApplication.run(App.class, args)` run,
- Spring says: “Okay, I will:
  - Create a container
  - Read your configuration
  - Find your classes
  - Create objects
  - Connect dependencies
  - Start the server
  - And make the app ready”
- Spring’s brain + memory
- Spring creates a central container (box) that will store and manage all your objects.
- When Spring creates the **ApplicationContext**, it:
  - Creates an empty container
  - Decides what type of app it is (web / non-web/ reactive)
  - Prepares to store beans

### 🟦 2. @SpringBootApplication

- find `D:\Jilani\learning\spring boot\springboot\springboot-2.md`

### 🟦 3. ALL bean definitions are now collected

- Collected from `@Configuration`, `@ComponentScan`, `@EnableAutoConfiguration`.

### 🟦 4. Bean lifecycle starts

#### 🔵4.1 Instantiate beans

- Now Spring starts creating beans:
  - Singleton beans are created eagerly
  - Prototype beans are created on demand

#### 🔵4.2 Inject dependencies

- **Spring resolves:**
  - @Autowired
  - Constructor injection
  - Setter injection
  - @Value

- **Order:**
  - Create bean
  - Inject dependencies
  - Resolve circular dependencies (if possible)

#### 🔵4.3 Lifecycle callbacks

- Spring invokes:
  - Aware interfaces
  - @PostConstruct
  - InitializingBean
  - Custom init methods

### 🟦 5. Application ready
