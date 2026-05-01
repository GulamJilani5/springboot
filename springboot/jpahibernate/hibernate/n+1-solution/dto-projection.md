⏺️ ➡️ 🟦 🟩 🟢 🔵 🔷 🔹🔴 ☑️ ✔️ ✓→•←⁕⁂※⁜‣

# ⏺️ Solution 3: DTO Projection in JPA

- DTO Projection in JPA means fetching only required fields from DB and mapping them directly into a DTO, instead of loading the full entity
- Improves performance (less data, no unnecessary joins)

##### 🔵 DTO

```java
public class UserDTO {
    private String name;
    private String email;

    public UserDTO(String name, String email) {
        this.name = name;
        this.email = email;
    }

    // getters
}
```

### 🟦 JPQL Constructor Expression

- **Key points:**
  - Use new keyword
  - Full package name of DTO is mandatory
  - Constructor must match fields

```java
@Repository
public interface UserRepository extends JpaRepository<User, Long> {

    @Query("SELECT new com.project.dto.UserDTO(u.name, u.email) FROM User u WHERE u.status = 'ACTIVE'")
    List<UserDTO> findActiveUsers();
}
```

### 🟦 Native Query with Projection

- Then manually map to DTO (not preferred)

```java
@Query(
  value = "SELECT name, email FROM users",
  nativeQuery = true
)
List<Object[]> getUsers();
```

### 🟦 Interface-Based Projection (Spring Data JPA)

```java
public interface UserProjection {
    String getName();
    String getEmail();
}
```

```java
@Query("SELECT u.name AS name, u.email AS email FROM User u")
List<UserProjection> getUsers();
```

- No constructor needed
- Spring maps automatically

### 🟦 When to Use DTO Projection

- Large entities (avoid fetching all columns)
- API response optimization
- Read-only queries
