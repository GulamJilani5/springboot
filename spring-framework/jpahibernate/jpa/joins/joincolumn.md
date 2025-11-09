⏺️ ➡️ 🟦 🔵 🟢🔴⭕🟠🟣🟥🟧✔️ ☑️ • ‣ → ⁕

# ⏺️ @JoinColumn

- **Used In:** `@OneToOne`, `@ManyToOne`, `@OneToMany` (**Owning Side**🔴 only)
- Defines the **Foreign Key**🔴 column in the database for the relationship.
- `@JoinColumn` tells **JPA/Hibernate** (or any JPA provider) which column in the source table holds the foreign key reference to the target entity's primary key.
- **Unidirectional** (one side knows about the other) and **Bidirectional** (both sides aware, with an owning side holding the **foreign key**).

## ➡️ @ManyToOne (Unidirectional)

- An **Order** belongs to exactly one Customer. The **FK** is in the orders table. This is unidirectional (Order knows Customer, but **Customer** doesn't know its Orders).

```java
  @Entity
@Table(name = "customers")
public class Customer {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(nullable = false)
    private String name;

    @Column(unique = true)
    private String email;

    // No relationship here (unidirectional)
    // Constructors, getters, setters...
}

@Entity
@Table(name = "orders")
public class Order {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(name = "order_date")
    private LocalDateTime orderDate;

    @Column(precision = 10, scale = 2)
    private BigDecimal total;

    // ManyToOne: Owning side with FK
    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(
        name = "customer_id",  // FK column in 'orders'
        referencedColumnName = "id",  // References 'id' in 'customers'
        nullable = false,  // Mandatory relationship
        foreignKey = @ForeignKey(name = "fk_order_customer")
    )
    private Customer customer;

    // Constructors, getters, setters...
}
```

- **Schema Impact**

```java
CREATE TABLE customers (
    id BIGINT PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    email VARCHAR(255) UNIQUE
);

CREATE TABLE orders (
    id BIGINT PRIMARY KEY,
    order_date TIMESTAMP,
    total DECIMAL(10,2),
    customer_id BIGINT NOT NULL,
    FOREIGN KEY (customer_id) REFERENCES customers(id)
);
```

## ➡️ @OneToMany (Unidirectional)

- A Customer has multiple Orders. The FK is not in customers (instead, we'd use a join table or duplicate FKs, but unidirectional OneToMany often uses @JoinTable for clarity). Here, we assume the FK is managed via the Order side (but Customer doesn't reference it).

```java
 // Customer (same as above, but add relationship)
@Entity
@Table(name = "customers")
public class Customer {
    // ... (id, name, email as above)

    // Unidirectional OneToMany: Uses join table (no FK in customers)
    @OneToMany(fetch = FetchType.LAZY)
    @JoinTable(
        name = "customer_orders",  // Join table
        joinColumns = @JoinColumn(name = "customer_id", referencedColumnName = "id"),
        inverseJoinColumns = @JoinColumn(name = "order_id", referencedColumnName = "id")
    )
    private List<Order> orders = new ArrayList<>();

    // Constructors, getters, setters...
}

// Order (same as ManyToOne above, but no @ManyToOne here for unidirectional)
@Entity
@Table(name = "orders")
public class Order {
    // ... (id, orderDate, total as above)
    // No reference to Customer (unidirectional from Customer side)
}
```

- Schema Impact

```java
 -- customers and orders as before, plus:
CREATE TABLE customer_orders (
    customer_id BIGINT,
    order_id BIGINT,
    PRIMARY KEY (customer_id, order_id),
    FOREIGN KEY (customer_id) REFERENCES customers(id),
    FOREIGN KEY (order_id) REFERENCES orders(id)
);
```

- Unidirectional OneToMany without a join table requires the inverse ManyToOne—prefer bidirectional for simplicity.

## ➡️ @OneToOne (Bidirectional)

- A Customer has one primary Address. The FK is in the addresses table (Address owns the relationship). Bidirectional: Both reference each other, but only one side persists the FK.

```java
  @Entity
@Table(name = "customers")
public class Customer {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(nullable = false)
    private String name;

    // OneToOne: Inverse side (no FK here)
    @OneToOne(mappedBy = "customer", cascade = CascadeType.ALL, fetch = FetchType.LAZY)
    private Address address;

    // Constructors, getters, setters...
    public void setAddress(Address address) {
        this.address = address;
        address.setCustomer(this);  // Sync bidirectional
    }
}

@Entity
@Table(name = "addresses")
public class Address {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(nullable = false)
    private String street;

    @Column(nullable = false)
    private String city;

    // OneToOne: Owning side with FK
    @OneToOne(fetch = FetchType.LAZY)
    @JoinColumn(
        name = "customer_id",  // FK in 'addresses'
        referencedColumnName = "id",
        nullable = false,  // Shared primary address
        unique = true,  // One address per customer
        foreignKey = @ForeignKey(name = "fk_address_customer")
    )
    private Customer customer;

    // Constructors, getters, setters...
}
```

- Schema Impact

```java
  CREATE TABLE customers (
    id BIGINT PRIMARY KEY,
    name VARCHAR(255) NOT NULL
);

CREATE TABLE addresses (
    id BIGINT PRIMARY KEY,
    street VARCHAR(255) NOT NULL,
    city VARCHAR(255) NOT NULL,
    customer_id BIGINT NOT NULL UNIQUE,
    FOREIGN KEY (customer_id) REFERENCES customers(id)
);
```

## ➡️ @ManyToMany (Bidirectional)

- A Customer can place Orders for multiple Products, and a Product can appear in multiple Orders. But to fit Customer-Order, we'll model Customer directly to Product via a join table (e.g., wishlist). @JoinTable defines the join columns.

```java
  @Entity
@Table(name = "customers")
public class Customer {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(nullable = false)
    private String name;

    // ManyToMany: Owning side
    @ManyToMany(cascade = {CascadeType.PERSIST, CascadeType.MERGE}, fetch = FetchType.LAZY)
    @JoinTable(
        name = "customer_products",  // Join table
        joinColumns = @JoinColumn(name = "customer_id", referencedColumnName = "id"),
        inverseJoinColumns = @JoinColumn(name = "product_id", referencedColumnName = "id"),
        foreignKey = @ForeignKey(name = "fk_customer_product_cust"),
        inverseForeignKey = @ForeignKey(name = "fk_customer_product_prod")
    )
    private Set<Product> products = new HashSet<>();  // Use Set to avoid duplicates

    // Constructors, getters, setters...
    public void addProduct(Product product) {
        this.products.add(product);
        product.getCustomers().add(this);  // Sync bidirectional
    }
}

@Entity
@Table(name = "products")
public class Product {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(nullable = false)
    private String name;

    @Column(precision = 8, scale = 2)
    private BigDecimal price;

    // ManyToMany: Inverse side (no FK or JoinTable here)
    @ManyToMany(mappedBy = "products")
    private Set<Customer> customers = new HashSet<>();

    // Constructors, getters, setters...
}
```

- Schema Impact

```java
  CREATE TABLE customers (
    id BIGINT PRIMARY KEY,
    name VARCHAR(255) NOT NULL
);

CREATE TABLE products (
    id BIGINT PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    price DECIMAL(8,2)
);

CREATE TABLE customer_products (
    customer_id BIGINT,
    product_id BIGINT,
    PRIMARY KEY (customer_id, product_id),
    FOREIGN KEY (customer_id) REFERENCES customers(id),
    FOREIGN KEY (product_id) REFERENCES products(id)
);
```
