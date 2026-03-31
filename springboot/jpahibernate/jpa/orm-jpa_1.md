🔵🟢🔴➡️⭕🟠🟦🟣🟥🟧✔️⏺️ ☑️ • ‣ → ⁕

# ➡️ ORM

Process of persisting(mapping) java objects directly into a db tables.

- **Example of ORM**
  - Hibernate, EclipseLink

# ➡️ JPA

- Provides specification for persisting, reading and managing data from java objects to relational table in the db.
- JPA is guidelines to implement ORM.
- JPA provides interfaces, annotations and rules for ORM.
- JPA itself has no working code, its just a standard
- `javax.persistence` or `jakarta.persistence`(newer version).

## 🟦 Is there any code for JPA

- JPA itself has no working code, its just a standard(blueprint).
- Needed a JPA provider like Hibernate to make it work in real applications.

## 🟦 JPA Components

- JPA consists mainly of **interfaces**, **annotations**, and **rules**.

#### 🔵 Interfaces

- These are part of the specification and represent the contract for ORM operations.
- These interfaces only define abstract methods. They don’t contain any implementation.

##### → EntityManager

- Core interface for interacting with the persistence context.

##### → EntityTransaction

- Manages transactions in resource-local setups.

##### → Query

- Represents database queries in a type-safe way.

##### for Example

- **EntityManager** is just an interface in JPA.
- **Hibernate** provides a class like **HibernateEntityManager** which actually executes SQL queries behind the scenes.

- So, without a provider, JPA alone cannot persist anything to the database.

#### 🔵 Annotations

- JPA provides annotations to map Java classes to database tables:

| Annotation                  | Purpose                                                   |
| --------------------------- | --------------------------------------------------------- |
| `@Entity`                   | Marks a class as a persistent entity.                     |
| `@Id`                       | Marks a field as the primary key.                         |
| `@Table`                    | Specifies the database table name.                        |
| `@Column`                   | Customizes column mapping (name, length, nullable, etc.). |
| `@GeneratedValue`           | Defines strategy for primary key generation.              |
| `@OneToMany` / `@ManyToOne` | Defines relationships between entities.                   |
| `@JoinColumn`               | Specifies the foreign key column.                         |

These annotations do not perform any work by themselves — they are metadata that the **JPA provider (e.g., Hibernate)** uses at **runtime**.

#### 🔵 Rules & Contracts

JPA defines rules for mapping Java objects to relational tables. For example:

- A class annotated with @Entity maps to a table.
- Fields map to columns.
- Relationships between classes map to foreign keys and join tables.
- Entity states and lifecycle rules (Transient, Persistent, Detached, Removed) are clearly defined.

These rules are **mandatory for all JPA providers** to follow, ensuring standardization across implementations.
