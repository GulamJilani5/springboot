⏺️ ➡️ 🟦 🔵 🟢🔴⭕🟠🟣🟥🟧✔️ ☑️ • ‣ → ⁕

# ⏺️ Mappings

## ➡️ One-to-One (1:1) Mapping

- Represents exclusive 1:1 relationships (e.g., `User ↔ Profile`). In **DB:** **Foreign key** in one table references the other's **PK**, or shared PK via `@MapsId/@PrimaryKeyJoinColumn`.
- **Unidirectional:** One entity navigates (e.g., `User → Address`); owning side defines `@JoinColumn`.
- **Bidirectional:** Both navigate; owning side (typically "many" or arbitrary) holds FK; inverse uses `@MappedBy` to avoid duplicate columns.
- **DB Schema:** E.g., user table with `address_id` **FK** (nullable if optional).
- **Cascades/Fetch:** `CascadeType.ALL` propagates ops; `FetchType.LAZY` (default **EAGER** in **JPA 2**, but **LAZY** recommended) delays loading.
- **Pitfalls:** Enforce uniqueness on FK with **@JoinColumn(unique=true)**; bidirectional requires manual sync to avoid inconsistencies. In **Spring**, lazy loading needs `@Transactional` to prevent **LazyInitializationException**.
- **Spring Boot/SDJPA Nuances:** Repos can use `@EntityGraph` for eager fetch; auditing with `@CreatedBy` integrates well.

### 🟦 Entities (Bidirectional; Address owns FK):

```java
  @Entity
public class User {
    @Id @GeneratedValue(strategy = GenerationType.IDENTITY) private Long id;
    private String name;

    @OneToOne(mappedBy = "user", cascade = CascadeType.ALL, fetch = FetchType.LAZY)
    private Address address;

    // Helper: public void setAddress(Address a) { address = a; a.setUser(this); }
    // Getters/setters
}

@Entity
public class Address {
    @Id @GeneratedValue(strategy = GenerationType.IDENTITY) private Long id;
    private String street;

    @OneToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "user_id", unique = true, nullable = false)
    private User user;

    // Getters/setters
}
```

### 🟦 Spring Data JPA Repository:

```java
  @Repository
public interface UserRepository extends JpaRepository<User, Long> {
    @EntityGraph(attributePaths = {"address"})  // Eager fetch address
    Optional<User> findByName(String name);

    @Query("SELECT u FROM User u LEFT JOIN FETCH u.address WHERE u.id = :id")
    Optional<User> findByIdWithAddress(@Param("id") Long id);
}
```

### 🟦 Spring Boot Service Usage:

```java
  @Service
@Transactional
public class UserService {
    @Autowired private UserRepository userRepository;

    public User createUserWithAddress(String name, String street) {
        User user = new User(name);
        Address addr = new Address(street);
        user.setAddress(addr);  // Sync both sides
        return userRepository.save(user);  // Cascades persist Address
    }

    public User getUserWithAddress(Long id) {
        return userRepository.findByIdWithAddress(id).orElseThrow();
    }
}
```

### 🟦 Controlle:

```java
`@PostMapping("/users") public User create(`@RequestBody UserDto dto`) { return userService.createUserWithAddress(dto.getName(), dto.getStreet()); }.
```

- **DB Output:** Saves user (id=1), address (id=1, user_id=1).

## ➡️ One-to-Many (1:N) Mapping

- **Hierarchical:** One parent owns many children (e.g., `User → Addresses`). **DB:** **FK** in child table (address.user_id); parent has no FK.
- **Unidirectional:** Parent holds `List<Child>` with `@JoinColumn` for shared **FK**.
- **Bidirectional:** Child holds `@ManyToOne` to parent (owning); parent uses `@MappedBy`.
- **DB Schema:** Child table has **FK** array (no collection column in parent).
- **Cascades/Fetch:** `orphanRemoval=true` auto-deletes removed items; LAZY default avoids loading all children upfront.
- **Pitfalls:** N+1 queries (fix with JOIN FETCH or `@EntityGraph`); bidirectional sync essential. In Spring Boot, pagination via Pageable on repos handles large collections.
- **Spring Boot/SDJPA Nuances:** Derived queries like **findByUserName** auto-join; `@BatchSize(size=10)` batches lazy loads.

```java

```

### 🟦 Entities (Bidirectional; Address owns):

```java
  @Entity
public class User {
    @Id @GeneratedValue(strategy = GenerationType.IDENTITY) private Long id;
    private String name;

    @OneToMany(mappedBy = "user", cascade = CascadeType.ALL, orphanRemoval = true, fetch = FetchType.LAZY)
    private List<Address> addresses = new ArrayList<>();

    // Helper: public void addAddress(Address a) { a.setUser(this); addresses.add(a); }
    // Getters/setters
}

@Entity
public class Address {
    @Id @GeneratedValue(strategy = GenerationType.IDENTITY) private Long id;
    private String street;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "user_id", nullable = false)
    private User user;

    // Getters/setters
}
```

### 🟦 Spring Data JPA Repository:

```java
   @Repository
public interface UserRepository extends JpaRepository<User, Long> {
    @EntityGraph(attributePaths = {"addresses"})
    Page<User> findByNameContaining(String name, Pageable pageable);  // Paginated with fetch

    @Query("SELECT u FROM User u LEFT JOIN FETCH u.addresses WHERE u.name = :name")
    List<User> findByNameWithAddresses(@Param("name") String name);
}
```

### 🟦 Spring Boot Service Usage:

```java
 @Service
@Transactional
public class UserService {
    @Autowired private UserRepository userRepository;

    public User createUserWithAddresses(String name, List<String> streets) {
        User user = new User(name);
        streets.forEach(st -> {
            Address addr = new Address(st);
            user.addAddress(addr);  // Sync
        });
        return userRepository.save(user);  // Cascades all
    }

    public Page<User> getUsersWithAddresses(String name, Pageable pageable) {
        return userRepository.findByNameContaining(name, pageable);
    }
}
```

- **DB Output:** User (id=1), Addresses (id=1/2, user_id=1 both).
- **Test:** Use `@DataJpaTest` with `userRepository.saveAndFlush(user)`.

## ➡️ Many-to-One (N:1) Mapping

- **Inverse of 1:N:** Many children reference one parent (e.g., `Addresses → User`). Often paired bidirectionally.
- **Unidirectional:** Child holds scalar reference (`@ManyToOne`); no collection on parent.
- **Bidirectional:** Parent uses `@OneToMany(mappedBy = "parentField")`.
- **DB Schema:** FK in "many" table only (e.g., `address.user_id`).
- **Cascades/Fetch:** No cascade on "many" side typically; **EAGER** default (switch to **LAZY**); use `optional=false` for required.
- **Pitfalls:** Circular refs in bidirectional (use `@JsonManagedReference/@JsonBackReference` for JSON serialization). In **SDJPA**, custom queries prevent over-fetching.
- **Spring Boot/SDJPA Nuances:** Repos derive `findByUserId`; projections (DTOs) fetch only **FK** without full entity.

### 🟦 Entities (Unidirectional for simplicity):

```java
  @Entity
public class User {
    @Id @GeneratedValue(strategy = GenerationType.IDENTITY) private Long id;
    private String name;

    // No collection here
}

@Entity
public class Address {
    @Id @GeneratedValue(strategy = GenerationType.IDENTITY) private Long id;
    private String street;

    @ManyToOne(fetch = FetchType.LAZY, optional = false)
    @JoinColumn(name = "user_id")
    private User user;

    // Getters/setters
}
```

### 🟦 Spring Data JPA Repository:

```java
  @Repository
public interface AddressRepository extends JpaRepository<Address, Long> {
    List<Address> findByUserId(Long userId);  // Derived: auto JPQL "WHERE user.id = ?"

    @Query("SELECT a FROM Address a JOIN a.user u WHERE u.name = :name")
    List<Address> findByUserName(@Param("name") String name);
}
```

### 🟦 Spring Boot Service Usage:

```java
  @Service
@Transactional
public class AddressService {
    @Autowired private AddressRepository addressRepository;
    @Autowired private UserRepository userRepository;  // Assume exists

    public Address createAddressForUser(Long userId, String street) {
        User user = userRepository.findById(userId).orElseThrow();
        Address addr = new Address(street);
        addr.setUser(user);
        return addressRepository.save(addr);  // No cascade needed
    }

    public List<Address> getAddressesByUserName(String name) {
        return addressRepository.findByUserName(name);
    }
}
```

- **DB Output:** Address (id=1, user_id=1).
- **Optimization:** Add `@QueryHints({@QueryHint(name = "org.hibernate.cacheable", value = "true")})` for caching.

## ➡️ Many-to-Many (N:N) Mapping

- Cross-associations (e.g., `Users ↔ Roles`). DB: Join table (e.g., user_roles with user_id, role_id); add extras via `@JoinColumns`.
- **Unidirectional:** One side holds Set<Other> with **@JoinTable**.
- **Bidirectional:** Both hold collections; owning defines **@JoinTable**, inverse **@MappedBy**.
- **DB Schema:** Auto-creates join table; use uniqueConstraints for composite keys.
- **Cascades/Fetch:** `CascadeType.PERSIST/MERGE` common (not **REMOVE**, to avoid mass deletes); **LAZY** default.
- **Pitfalls:** Self-joins or cycles (use `@JsonIgnore`); large join tables need indexing. Sync both sides strictly.
- **Spring Boot/SDJPA Nuances:** Repos support **findByRolesIdIn**; `@Query` for complex joins; embeddable classes fo- join table extras (e.g., enrollment date).

### 🟦 Entities (Bidirectional; User owns):

```java
  @Entity
public class User {
    @Id @GeneratedValue(strategy = GenerationType.IDENTITY) private Long id;
    private String name;

    @ManyToMany(mappedBy = "users", fetch = FetchType.LAZY)
    private Set<Role> roles = new HashSet<>();

    // No @JoinTable here
}

@Entity
public class Role {
    @Id @GeneratedValue(strategy = GenerationType.IDENTITY) private Long id;
    private String name;

    @ManyToMany(fetch = FetchType.LAZY)
    @JoinTable(name = "user_roles",
               joinColumns = @JoinColumn(name = "role_id"),
               inverseJoinColumns = @JoinColumn(name = "user_id"))
    private Set<User> users = new HashSet<>();

    // Helper: public void addUser(User u) { u.getRoles().add(this); users.add(u); }
}
```

### 🟦 Spring Data JPA Repository:

```java
  @Repository
public interface UserRepository extends JpaRepository<User, Long> {
    @EntityGraph(attributePaths = {"roles"})
    Optional<User> findByName(String name);

    @Query("SELECT u FROM User u JOIN FETCH u.roles r WHERE r.name = :roleName")
    List<User> findByRoleName(@Param("roleName") String roleName);
}
```

### 🟦 Spring Boot Service Usage:

```java
  @Service
@Transactional
public class UserRoleService {
    @Autowired private UserRepository userRepository;

    public User assignRoleToUser(Long userId, Long roleId) {
        User user = userRepository.findById(userId).orElseThrow();
        Role role = roleRepository.findById(roleId).orElseThrow();  // Assume repo
        user.getRoles().add(role);  // Sync (helper does both)
        return userRepository.save(user);  // Persists join rows
    }

    public List<User> getUsersByRole(String roleName) {
        return userRepository.findByRoleName(roleName);
    }
}
```

- **DB Output:** user_roles table: rows (user_id=1, role_id=1).
- **Advanced:** For extra join columns, use `@Embeddable` class with `@CollectionTable`.
