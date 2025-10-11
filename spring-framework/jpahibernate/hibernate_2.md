🔵🟢🔴➡️⭕🟠🟦🟣🟥🟧✔️⏺️ ☑️ • ‣ → ⁕

⏺️ Lazy vs Eager Loading in Hibernate + Avoiding LazyInitializationException

# ➡️ Eager Loading

- Associated entities are loaded immediately along with the parent entity, typically using JOINs in a single query.
- **Default Behavior:** Eager for @ManyToOne and @OneToOne (non-optional side).
- **Advantages:** Avoids additional queries; all data is available upfront, preventing exceptions in detached states.
- **Disadvantages:**
  - Can load unnecessary data, leading to higher memory use and slower initial queries (Cartesian products in complex graphs).
  - Can lead to performance issues (N+1 problem, unnecessary joins).
- Configured with `fetch = FetchType.EAGER`.

```java
    @Entity
    public class Department {
        @OneToMany(mappedBy = "department", fetch = FetchType.EAGER)
        private List<Employee> employees;
    }

```

- When **Department** is fetched, all **Employees** are also loaded immediately.

# ➡️ Lazy Loading

- Associations are not loaded immediately when the parent entity is fetched. Instead, a proxy object is created, and the actual data is loaded only when accessed (e.g., via a getter).
- **Default Behavior:** Lazy is the default for collection types (**@OneToMany, @ManyToMany**) in Hibernate/JPA. For single-valued associations (**@OneToOne, @ManyToOne**), the default is eager.
- **Advantages:** Reduces initial query overhead and memory usage, especially for large graphs. Improves performance by loading only what's needed.
- **Disadvantages:** Can lead to N+1 select problems (multiple queries fired on access) and LazyInitializationException if accessed outside the session.
- Configured with `fetch = FetchType.LAZY`.

```java
    @Entity
    public class Department {
        @OneToMany(mappedBy = "department", fetch = FetchType.LAZY)
        private List<Employee> employees;
    }

```

- If you close the session and then try to access **department.getEmployees()**, you’ll get:

```java
org.hibernate.LazyInitializationException: could not initialize proxy

```

- But session is not closed upon Selecting a **Department** loads only **Department** data; accessing `department.getOrders()` triggers a separate **SELECT** for **employees**.

## ➡️ How to Avoid LazyInitializationException:
