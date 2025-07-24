# 🪸 Bean
- A Bean in Spring is simply a Java object that is managed by the Spring IoC container.
- A bean is any Java object registered with the Spring container, which can then be injected into other beans as dependencies.
- When you declare a class as a bean, you’re telling Spring:
  `➔ “I want you to create, initialize, configure, and manage the lifecycle of this object.”`
  **🧠 Use Case:** Used to define services, repositories, or components that Spring should manage automatically. It ensures dependency injection, singleton scope, etc.
**Annotations:** `@Component`, `@Service`, `@Repository`, `@Bean`.

### 🔗 @Autowired?
- **@Autowired** is an annotation in Spring that tells the Spring container to automatically inject a bean into another bean’s field, constructor, or setter method.
- In simpler terms: it wires dependencies between beans, so you don’t have to do it manually.

# 🧍‍♂️ Entity
An Entity is a POJO (Plain Old Java Object) class that is mapped to a database table using JPA/Hibernate. Each instance represents a row in the table.
**🧰Use Case:** Entities are used in the persistence layer. They represent the actual data stored in the database.

