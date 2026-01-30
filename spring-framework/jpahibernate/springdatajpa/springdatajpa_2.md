🔵🟢🔴➡️⭕🟠🟦🟣🟥🟧✔️⏺️ ☑️ • ‣ → ⁕

# ⏺️ Query creation: derived queries and native SQL(@Query)

## ➡️ Inbuilt Repository Methods

- from JpaRepository<T, ID>

```java
// Save
<S extends T> S save(S entity);
<S extends T> List<S> saveAll(Iterable<S> entities);
<S extends T> S saveAndFlush(S entity);

// Read
Optional<T> findById(ID id);
boolean existsById(ID id);
List<T> findAll();
List<T> findAllById(Iterable<ID> ids);
long count();

// Delete
void deleteById(ID id);
void delete(T entity);
void deleteAllById(Iterable<? extends ID> ids);
void deleteAll(Iterable<? extends T> entities);
void deleteAll();

// Batch
void flush();
void deleteAllInBatch();
void deleteAllByIdInBatch(Iterable<ID> ids);


//Pagination & Sorting
Page<T> findAll(Pageable pageable);
List<T> findAll(Sort sort);
```

## ➡️ Derived query methods:

- Derived queries allow Spring Data JPA to automatically generate JPQL queries based on the method name in your repository interface.

- This is ideal for straightforward CRUD operations and filtering without writing explicit query strings. The framework parses the method name using the JPA 🔴 Criteria API to build the query.

### 🟦 Basic Finders

```java
Optional<User> findByEmail(String email);
List<User> findByStatus(String status);
User getByEmail(String email);
User readByEmail(String email);

```

### 🟦 Exists & Count

```java
boolean existsByEmail(String email);
long countByStatus(String status);

```

### 🟦 AND / OR / NOT

```java
List<User> findByEmailAndStatus(String email, String status);
List<User> findByRoleOrStatus(String role, String status);
List<User> findByStatusNot(String status);

```

### 🟦 String Matching

```java
List<User> findByNameContaining(String name);
List<User> findByNameLike(String pattern);
List<User> findByNameStartingWith(String prefix);
List<User> findByNameEndingWith(String suffix);
List<User> findByEmailIgnoreCase(String email);

```

### 🟦 Comparison Operators

```java
List<User> findByAgeGreaterThan(Integer age);
List<User> findByAgeGreaterThanEqual(Integer age);
List<User> findByAgeLessThan(Integer age);
List<User> findByAgeLessThanEqual(Integer age);
List<User> findByAgeBetween(Integer start, Integer end);

```

### 🟦 Date Queries

```java
List<User> findByCreatedDateAfter(LocalDate date);
List<User> findByCreatedDateBefore(LocalDate date);
List<User> findByCreatedDateBetween(LocalDate start, LocalDate end);

```

### 🟦 Boolean Conditions

```java
List<User> findByActiveTrue();
List<User> findByActiveFalse();

```

### 🟦 NULL Checks

```java
List<User> findByEmailIsNull();
List<User> findByEmailIsNotNull();

```

### 🟦 IN / NOT IN

```java
List<User> findByStatusIn(List<String> statuses);
List<User> findByStatusNotIn(List<String> statuses);

```

### 🟦 Sorting & Limiting

```java
List<User> findTop3ByOrderByCreatedDateDesc();
User findFirstByStatusOrderByIdAsc(String status);

```

### 🟦 Delete / Remove

```java
void deleteByEmail(String email);
long deleteByStatus(String status);
void removeByEmail(String email);

```

### 🟦 Complex Combined Method

```java
List<User> findTop5ByStatusAndAgeGreaterThanOrderByCreatedDateDesc(
        String status,
        Integer age
);

```

## ➡️ Custom Query(JPQL) vs native queries:

- All native queries are custom queries, but not all custom queries are native. If you're writing `@Query` without `nativeQuery = true`, it's custom **JPQL**. Use native only when you need SQL's full power.

##### 🔵 Similarities

- Both are defined using the `@Query` annotation in your repository interface.
- They're used when derived queries (method-name-based) aren't sufficient for complex logic.
- They support parameters (positional ?1 or named :param), sorting (Sort), pagination (Pageable), and modifying operations (`@Modifying`).
- Both override any auto-generated query from the method name

##### 🔵 Differences

| **Aspect**              | **Custom Query (JPQL)**                                                                                                                                            | **Native Query (SQL)**                                                                                                                                                                               |
| ----------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Default Language**    | **JPQL** (Java Persistence Query Language) — object-oriented, works with **entities and their fields**.<br>✅ Example: `SELECT u FROM User u`                      | **Raw SQL** — works directly on **database tables and columns**.<br>✅ Example: `SELECT * FROM users`                                                                                                |
| **How to Define**       | Use the `@Query` annotation with JPQL syntax. <br>`java\n@Query("SELECT u FROM User u WHERE u.email = :email")\nUser findByEmail(@Param("email") String email);\n` | Use the `@Query` annotation with `nativeQuery = true`. <br>`java\n@Query(value = "SELECT * FROM users WHERE email = :email", nativeQuery = true)\nUser findByEmail(@Param("email") String email);\n` |
| **Portability**         | ✅ **High** — JPQL is database-agnostic; queries work across different databases without modification.                                                             | ⚠️ **Low** — Native SQL is **tied to specific SQL dialects** (e.g., MySQL vs. PostgreSQL syntax differences).                                                                                        |
| **Entity Mapping**      | ✅ **Automatic** — Returns **entities** or **projections** naturally, thanks to ORM mapping.                                                                       | ⚠️ You must ensure **manual mapping** matches the entity structure. Can return raw objects, projections, or `Map<String, Object>`.                                                                   |
| **Use Cases**           | - Complex joins and aggregations involving entities<br>- Entity-specific filtering or business logic<br>- When you want DB independence                            | - Leveraging **database-specific features** (e.g., window functions, CTEs)<br>- When JPQL **lacks support** for certain SQL constructs<br>- Performance-tuned queries                                |
| **Performance**         | Slightly slower due to JPQL-to-SQL translation by the JPA provider (Hibernate).                                                                                    | Potentially faster — queries are executed **as-is**, bypassing JPQL parsing. But can become **harder to maintain**.                                                                                  |
| **Escaping / Security** | ✅ JPQL uses **parameter binding by default**, which **prevents SQL injection**.                                                                                   | Same parameter binding is available, but you must be **extra careful** with string concatenation or dynamic native queries.                                                                          |

### 🟦 Custom Query(JPQL)

- JPQL (Java Persistence Query Language) is a database-independent, object-oriented query language defined by JPA.
- 🔴🔴🔴🔴🔴You write queries using **Entity names** and **Entity field names**, not table or column names and whereas nativ query works at db level(directly intract with the db ike sql query)
- JPQL queries are translated by JPA/Hibernate into the appropriate SQL for your database.
- Works across databases (portable).

```java
public interface UserRepository extends JpaRepository<User, Long> {

    @Query("SELECT u FROM User u WHERE u.email = ?1")
    User findByEmail(String email);
}

```

- JPA will translate it into something like:

```java
SELECT * FROM users WHERE email = 'xyz@example.com';

```

##### 🔵 JPQL Query Features

- Uses Entity and field names
- Supports:
  - **SELECT**, **UPDATE**, **DELETE** (no **INSERT**)
  - Joins using entity relationships (**JOIN FETCH**)
  - Aggregations (**COUNT**, **MAX**, etc.)
  - Named parameters (**:param**) and positional (**?1**)
  - Portable between different databases
  - Automatic mapping to entity classes
- **Example 1: Simple Select**

```java
@Query("SELECT u FROM User u WHERE u.status = :status")
List<User> findByStatus(@Param("status") String status);

```

- **Example 2: Using Joins**
  - `u.roles` refers to the entity relationship (e.g., `@OneToMany`) — not tables.

```java
    @Query("SELECT u FROM User u JOIN u.roles r WHERE r.name = :roleName")
    List<User> findByRoleName(@Param("roleName") String roleName);

```

- **Example 3: Update (JPQL supports UPDATE/DELETE)**

```java
@Modifying
@Transactional
@Query("UPDATE User u SET u.status = :status WHERE u.id = :id")
int updateStatus(@Param("id") Long id, @Param("status") String status);

```

### 🟦 Native Queries

- A Native SQL query is a raw SQL query written in the database’s SQL dialect.
  - We use actual table and column names.
  - We set nativeQuery = true in the @Query annotation.
  - We used when JPQL isn’t flexible or performant enough.

```java
public interface UserRepository extends JpaRepository<User, Long> {

    @Query(value = "SELECT * FROM users WHERE email = ?1", nativeQuery = true)
    User findByEmailNative(String email);
}
```

- `users` is the actual table name in DB.
- `email` is the actual column name.

##### 🔵 JPQL Query Features

- Uses actual SQL
- Database-specific (non-portable)
- Can use any SQL feature your DB supports (functions, CTEs, stored procs, etc.)
- You can do SELECT, INSERT, UPDATE, DELETE
- Results can map to:
  - Entity classes (if the query selects all entity fields)
  - DTO / Projection interfaces
  - `Object[]` for custom columns

- **Example 1: Simple Select**

```java
@Query(value = "SELECT * FROM users WHERE status = :status", nativeQuery = true)
List<User> findByStatusNative(@Param("status") String status);

```

- **Example 2: Custom Columns**

```java
@Query(value = "SELECT name, email FROM users WHERE active = true", nativeQuery = true)
List<Object[]> findActiveUsersBasic();

```

- ***

```java
@Modifying
@Transactional
@Query(value = "UPDATE users SET status = :status WHERE id = :id", nativeQuery = true)
int updateUserStatusNative(@Param("id") Long id, @Param("status") String status);

```

- **Example 3: Update**

```java
@Modifying
@Transactional
@Query(value = "INSERT INTO users (name, email, status) VALUES (:name, :email, :status)", nativeQuery = true)
int insertUser(@Param("name") String name, @Param("email") String email, @Param("status") String status);

```

- **Example 4: Insert**

```java
@Modifying
@Transactional
@Query(value = "INSERT INTO users (name, email, status) VALUES (:name, :email, :status)", nativeQuery = true)
int insertUser(@Param("name") String name, @Param("email") String email, @Param("status") String status);

```
