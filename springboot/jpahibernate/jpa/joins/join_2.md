⏺️ ➡️ 🟦 🔵 🟢🔴⭕🟠🟣🟥🟧✔️ ☑️ • ‣ → ⁕

# ⏺️ @JoinColumn, @JoinTable, @MappedBy, @Embedded and @Embeddable

### ➡️ @Embeddable

- Used on a class to tell JPA:
  - This class is NOT a separate entity/table.
  - Its fields should be embedded(added) inside another entity’s table.

### ➡️ @Embedded

- Used inside an Entity to tell JPA:
  - “Store the fields of this `@Embeddable` class INSIDE my table.

##### 🟦 Create an embeddable class

```java
@Embeddable
public class Address {
    private String street;
    private String city;
    private String state;
    private String pinCode;
}

```

- This class will NOT create an address table.

##### 🟦 Use it in an Entity

```java
  @Entity
public class Employee {

    @Id
    @GeneratedValue
    private Long id;

    private String name;

    @Embedded
    private Address address;
}

```

- Resulting **employee** table:

| id  | name  | street  | city | state | pinCode |
| --- | ----- | ------- | ---- | ----- | ------- |
| 1   | Gulam | MG Road | Pune | MH    | 411001  |

- No Address table is created
- `@Embeddable` Class' Fields are flattened(added) inside **employee** table via `@embedded`.
