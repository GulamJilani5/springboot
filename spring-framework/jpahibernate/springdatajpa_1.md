🔵🟢🔴➡️⭕🟠🟦🟣🟥🟧✔️⏺️ ☑️ • ‣ → ⁕

# ⏺️ Spring Data JPA

- Spring Data JPA provides an abstraction layer on top of **JPA/Hibernate**, eliminating boilerplate code for common database operations.

## ➡️ JPA vs Spring Data JPA

### 🟦 JPA Repository Without Spring Data JPA

- Normally, without Spring Data JPA, you would manually create a repository for each entity using EntityManager:

```java

public class UserRepository {

    @PersistenceContext
    private EntityManager entityManager;

    public User findById(Long id) {
        return entityManager.find(User.class, id);
    }

    public void save(User user) {
        entityManager.persist(user);
    }

    public void delete(User user) {
        entityManager.remove(user);
    }

    public List<User> findAll() {
        return entityManager.createQuery("SELECT u FROM User u", User.class)
                            .getResultList();
    }
}
```

- This pattern is repeated for every entity → ProductRepository, OrderRepository, etc.
- It leads to a lot of repetitive, boilerplate code.

### 🟦 JPA Repository With Spring Data JPA

- With Spring Data JPA, you simply define an interface

```java
public interface UserRepository extends JpaRepository<User, Long> {
}

```

- Spring Data JPA automatically generates the implementation at runtime.
- We instantly get methods like:
  - `findById()`
  - `findAll()`
  - `save()`
  - `delete()`

#### We can also create custom queries declaratively:

```java
List<User> findByEmail(String email);

```

- Spring Data JPA parses the method name and creates the SQL for you.
- Or using @Query:

```java
@Query("SELECT u FROM User u WHERE u.status = :status")
List<User> findByStatus(@Param("status") String status);

```

## ➡️ Spring Data JPA Basic concepts

### 🟦 Dependency

```java
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>
```

### 🟦 application.properties

```java
spring.datasource.url=jdbc:h2:mem:testdb
spring.datasource.username=sa
spring.datasource.password=
spring.jpa.hibernate.ddl-auto=update   # dev only; beware in prod
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.format_sql=true

```

- `datasource.url:` Points to an in-memory database named `testdb`
- `spring.jpa.hibernate.ddl-auto:` controls schema generation (validate/update/create/create-drop).

### 🟦 Entities & mappings (JPA basics)

```java
@Entity
@Table(name = "customers")
public class Customer {
  @Id
  @GeneratedValue(strategy = GenerationType.SEQUENCE) // or IDENTITY/UUID depending on DB
  private Long id;

  @Column(nullable = false)
  private String firstName;

  private String lastName;

  @OneToMany(mappedBy = "customer", cascade = CascadeType.ALL, fetch = FetchType.LAZY)
  private List<Order> orders = new ArrayList<>();

  @PrePersist
  public void prePersist() { /* e.g. set defaults */ }
}
```

- `@Entity` + `@Id` are required.
- `@GeneratedValue` strategy matters (SEQUENCE works better for batching than IDENTITY).
- 🔴**Fetch types:** **LAZY** for collections (recommended), **EAGER** for small single-valued relationships only when appropriate.
- **Cascade:** controls propagation of operations (`PERSIST`, `MERGE`, `REMOVE`, etc.).
  - Use carefully — `CascadeType.ALL` on large graphs can cause surprises.
- For **bi-directional** relationships keep `mappedBy` on the **non-owning** side and update both sides in your code to keep consistency.

### 🟦 Spring Data repositories — the core idea

- Define an interface, extend the right Spring Data interface — Spring provides implementation automatically.
- Common interfaces:
  - CrudRepository<T, ID> — basic CRUD
  - PagingAndSortingRepository<T, ID> — adds pagination & sorting
  - JpaRepository<T, ID> — full JPA features (flush, batch deletes, etc.)

```java
public interface CustomerRepository extends JpaRepository<Customer, Long> {
  List<Customer> findByLastName(String lastName);
  Page<Customer> findByActiveTrue(Pageable pageable);
}
```

- Defining repository interfaces and method signatures is the central pattern.

### 🟦 Query creation: derived queries and native SQL(@Query)

##### 🔵 Derived query methods:

##### 🔵 Custom JPQL or native queries with @Query:
