# 🪸 Bean

A Bean in Spring is simply a Java object that is managed by the Spring IoC container. Beans are the building blocks of Spring applications.

**🧠 Use Case:** Used to define services, repositories, or components that Spring should manage automatically. It ensures dependency injection, singleton scope, etc.

**Annotations:** `@Component`, `@Service`, `@Bean`.


# 🧍‍♂️ Entity
An Entity is a POJO (Plain Old Java Object) class that is mapped to a database table using JPA/Hibernate. Each instance represents a row in the table.

**🧰Use Case:** Entities are used in the persistence layer. They represent the actual data stored in the database.

# 📦 DTO (Data Transfer Object)
A DTO is a simple class used to transfer data between layers (like controller ↔ service). It does not contain any business logic or database mapping.

**🧰 Use Case:** 
- Improves security (exposes only needed fields), 
- Helps decouple internal domain model from API.
- Makes data serialization/deserialization easier.

# 🛠️ ORM (Object-Relational Mapping)
ORM is a technique that allows you to map Java objects to database tables. In Java, this is done using libraries like Hibernate/JPA.

**🧪 Example:**
    Using JPA annotations like @Entity, @Table, @Column, etc., lets the ORM framework handle SQL under the hood:

**🧰 Use Case:**
ORM eliminates boilerplate SQL code. It enables CRUD operations using Java methods, improves productivity, and supports lazy loading, cascading, etc.