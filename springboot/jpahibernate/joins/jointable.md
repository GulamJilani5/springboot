⏺️ ➡️ 🟦 🔵 🟢🔴⭕🟠🟣🟥🟧✔️ ☑️ • ‣ → ⁕

# ⏺️ @JoinTable — Used to Define a Join Table

- Typically used on the owning side of a `@ManyToMany` or `@OneToMany` relationship.
- To customize the name of the join table and columns.
- Control foreign key column names and constraints.

```java
    @Entity
    public class Student {
    @Id
    private Long id;
    @ManyToMany
    @JoinTable(
        name = "student_course", // Name of the join table
        joinColumns = @JoinColumn(name = "student_id"), // FK to Student
        inverseJoinColumns = @JoinColumn(name = "course_id") // FK to Course
    )
    private List<Course> courses;
}
```

## ➡️ When Do We Use @JoinTable

### 🟦 When you want the “One” side to own a One-to-Many relationship

- Why is **Order** the **Owning Side** (in JPA) when **Customer** is the **Parent** (Conceptually)?
  - Because **Hibernate** needs one side to “own” the relation to avoid duplicate foreign keys.
  - The **FK** column `customer_id` physically lives in the orders table.
- So **Order** controls updates to that relationship.

- **If Customer Becomes the Owning Side — What Changes?**
- **Normally, in a One-to-Many:**
  - FK goes in the Many side (Order table)
  - So Order becomes the owning side with @JoinColumn
- **But if you say:**
  - “No, I want Customer (One side) to own the relationship
- Then the FK cannot remain in the Order table (because then Order would still be the owner). To shift ownership to Customer, Hibernate must create a separate Join Table.
- **Why?**
  - Because **Customer** table (One side) cannot store multiple **FK** references in one column.
  - So **Hibernate** uses a **Join** Table like this:

```java
| customer_order               |
| ---------------------------- |
| customer_id (FK to customer) |
| order_id (FK to order)       |

```

- This table maps `customers ↔ orders`.

##### 🔵 Customer as Owning Side Using @JoinTable

```java
  @Entity
class Customer {

    @OneToMany
    @JoinTable(
        name = "customer_orders",
        joinColumns = @JoinColumn(name = "customer_id"),   // FK to Customer
        inverseJoinColumns = @JoinColumn(name = "order_id") // FK to Order
    )
    private List<Order> orders = new ArrayList<>();
}

```

```java
  @Entity
  class Order {
    @Id
    @GeneratedValue
    private Long id;

    private String product;
  }

```

- What happens in DB?
- customer table
  | id | name |
  | -- | ---- |
- order table
  | id | product |
  | -- | ------- |

- customer_orders (Join Table)
  | customer_id | order_id |
  | ----------- | -------- |

### 🟦 For Many-to-Many relationships (most common use)

- Because in Many-to-Many, neither side can hold a foreign key column to represent multiple values, so a join table is mandatory.

```java
  @ManyToMany
  @JoinTable(name = "student_course",
    joinColumns = @JoinColumn(name = "student_id"),
    inverseJoinColumns = @JoinColumn(name = "course_id"))
  private List<Course> courses;

```

### 🟦 To customize the join table name or join columns

- Even if Hibernate creates a join table automatically, you can rename it or change column names using `@JoinTable`.

### 🟦 When avoiding FK column in child table for design/performance reasons

- Some DB designers prefer not to put foreign keys in large tables (like Orders), so they opt for join tables.

## ➡️ When NOT to use @JoinTable

- Avoid it for simple `One-to-Many` if you want:
- Efficient joins
- Simple DB design
- Logical **FK** stored in the child table
- Because an extra join table adds unnecessary complexity.

## ➡️

| Mapping Type  | Supports `@JoinTable`? | Common Use Case                                                    |
| ------------- | ---------------------- | ------------------------------------------------------------------ |
| `@ManyToMany` | ✅ **Yes**             | Required to create join table between entities.                    |
| `@OneToMany`  | ✅ _Yes, but rare_     | Optional – used when no foreign key is stored in the child entity. |
| `@OneToOne`   | ✅ _Yes, but rare_     | Can be used if you want a join table (unusual).                    |
| `@ManyToOne`  | ❌ **No**              | Does **not** support `@JoinTable`. Use `@JoinColumn`.              |

- **Why @JoinTable Doesn’t Work with @ManyToOne**
  - **@ManyToOne** always means the foreign key is in the current entity’s table, so we use `@JoinColumn`,
    not a separate join table.
