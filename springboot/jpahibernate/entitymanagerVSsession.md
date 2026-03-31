⏺️ ➡️ 🟦 🔵 🟢🔴⭕🟠🟣🟥🟧✔️ ☑️ • ‣ → ⁕

# ⏺️ EntityManager & Session

- `EntityManager` = **Standard JPA API** (generic, portable)
- `Session` = **Hibernate-specific API** (extended, powerful)
- In Spring Boot, even if you use **JPA repositories**, under the hood, it’s Hibernate’s Session that executes queries.

### ➡️ Difference Between EntityManager and Session

| Aspect                   | **`EntityManager` (JPA)**                                                             | **`Session` (Hibernate)**                                                                                                 |
| :----------------------- | :------------------------------------------------------------------------------------ | :------------------------------------------------------------------------------------------------------------------------ |
| **Definition**           | It’s the **standard JPA interface** used to interact with the persistence context.    | It’s **Hibernate’s proprietary implementation** of `EntityManager` — extends its features.                                |
| **Standard vs Vendor**   | Part of **JPA Specification (javax.persistence)** — **vendor-independent**.           | Part of **Hibernate API (org.hibernate)** — **vendor-specific**.                                                          |
| **Creation**             | Obtained via `EntityManagerFactory`.                                                  | Obtained via `SessionFactory`.                                                                                            |
| **Persistence Context**  | Manages entity states (transient, persistent, detached, removed).                     | Same, but with **extra advanced features** like filters, scrollable results, etc.                                         |
| **Transaction Handling** | Works with `EntityTransaction` (for non-Spring apps) or `@Transactional` (in Spring). | Uses `Transaction` interface of Hibernate.                                                                                |
| **Methods**              | CRUD: `persist()`, `merge()`, `remove()`, `find()`, `createQuery()`, etc.             | All `EntityManager` methods plus Hibernate-specific ones like `save()`, `update()`, `saveOrUpdate()`, `createCriteria()`. |
| **Query Language**       | Uses **JPQL** (Java Persistence Query Language).                                      | Can use **JPQL + HQL + Native SQL**.                                                                                      |
| **Caching**              | Uses **Persistence Context** and optional **Second-Level Cache**.                     | Same — but gives **fine-grained control** over 1st/2nd level caches.                                                      |
| **Portability**          | Portable across any JPA implementation (EclipseLink, OpenJPA, etc.).                  | Tightly coupled to Hibernate (not portable).                                                                              |
| **Usage in Spring Boot** | Commonly used — Spring Data JPA internally uses `EntityManager`.                      | Can be unwrapped from `EntityManager`: `entityManager.unwrap(Session.class)` if needed.                                   |

### ➡️ Entity Manager, Session and Persistent Context

| Concept                     | Description                                               |
| --------------------------- | --------------------------------------------------------- |
| **EntityManager / Session** | Interface to interact with the persistence context        |
| **Persistence Context**     | In-memory container that tracks entity states and changes |
| **First-Level Cache**       | Built-in cache inside persistence context                 |
| **Second-Level Cache**      | Optional Hibernate-wide cache shared across sessions      |
