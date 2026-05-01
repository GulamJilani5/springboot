⏺️ ➡️ 🟦 🔵 🟢🔴⭕🟠🟣🟥🟧✔️ ☑️ • ‣ → ⁕

# ⏺️ Owner Side (Owning Side), Inverse Side (Non-Owning Side), Unidirectional Relationship, Bidirectional Relationship

## ➡️ Owner Side (Owning Side)

- “Owner side is the one who holds the key of the relationship.”
- The owner side is the side of the relationship that controls the foreign key (**FK**) in the database.
- It is the ONLY side JPA uses when saving/updating the relationship
- The owner side writes/updates the relationship in the DB.
- It must use `@JoinColumn` or `@JoinTable`.

- magine a marriage relationship:
  - If Husband keeps the house keys, he “owns” the house access.
  - The Wife knows she is related, but she doesn’t have the keys.
- Similarly, only owner side has the FK key.

```java
  import jakarta.persistence.*;

@Entity
public class Comment {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String review;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "post_id") // 🔥 FK lives here
    private Post post;

    // getters & setters
}

```

- Here, **Order** is the owner because it stores the **FK** `customer_id`.

## ➡️ Inverse Side (Non-Owning Side)

- The inverse side “knows about the relationship but does not control it.”
- The inverse side is the opposite side of the relationship, which does not own the foreign key.
- It only refers to the owner using `mappedBy`.

```java
import jakarta.persistence.*;
import java.util.ArrayList;
import java.util.List;

@Entity
public class Post {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String title;

    @OneToMany(mappedBy = "post", cascade = CascadeType.ALL, orphanRemoval = true)
    private List<Comment> comments = new ArrayList<>();

    // 🔥 Helper method (VERY IMPORTANT)
    public void addComment(Comment comment) {
        comments.add(comment);
        comment.setPost(this);
    }

    public void removeComment(Comment comment) {
        comments.remove(comment);
        comment.setPost(null);
    }

    // getters & setters
}
```

- `Customer` is inverse side (does not have FK).
- `mappedBy` = "customer" tells JPA that the FK exists in `Order`.

### 🟦 Owner vs Inverse 🔴

| Owner Side       | Inverse Side       |
| ---------------- | ------------------ |
| Controls FK      | Just references it |
| Uses @JoinColumn | Uses mappedBy      |
| Writes data      | Reads data         |

- 🔴Can a relationship exist without an inverse side?
  - Yes. If it's unidirectional, only one side exists.

## ➡️ Unidirectional Relationship

- Only one entity knows about the relationship.
- **Example:** `Customer → Order` (Customer knows Orders, Order does not know Customer)

```java
  @OneToMany
@JoinColumn(name = "customer_id")  // FK in orders table
private List<Order> orders;

```

- Here only **Customer** knows **Orders**.
- **Order** does not know **Customer**.

### 🟦 Real Life Example

- You have your friend’s contact number,
- but your friend does not have yours → One-way relationship.

## ➡️ Bidirectional Relationship

- Both entities know about each other.
- Navigation works in both directions.

- **Owner side (Order):**

```java
  @ManyToOne
@JoinColumn(name = "customer_id")
private Customer customer;    // Owner

```

- **Inverse side (Customer):**

```java
  @OneToMany(mappedBy = "customer")
private List<Order> orders;    // Inverse

```

- **Order** is the owner.
- **Customer** is the inverse with `mappedBy`.

### 🟦 Real Life Example

- You save your friend’s number
- and your friend also saves your number → two-way relationship.
