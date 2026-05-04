⏺️ ➡️ 🟦 🔵 🟢🔴⭕🟠🟣🟥🟧✔️ ☑️ • ‣ → ⁕

# ⏺️ Cascade & Orphan Removal In JPA

- In JPA/Hibernate, Cascade defines how operations performed on a parent entity should automatically be applied to its related (child) entities.
- **Example:**
  - If you delete a Customer, should all their Orders also be deleted automatically?
    Cascading helps define this behavior.
- **Cascade** = `Automatic Propagation of Entity State Changes to Associated Entities.`

- **Cascading** is specified using the `@Cascade` annotation (in Hibernate) or the cascade attribute in JPA relationship annotations like **@OneToMany** or **@ManyToOne** etc.
- Without cascading, operations on the parent won't affect children, potentially leading to errors like **EntityNotFoundException** or **orphaned records**.

### ➡️ Cascade Types in JPA

- **PERSIST** → Save children
- **MERGE** → Update children
- **REMOVE** → Delete children
- **REFRESH** → Reload children
- **DETACH** → Untrack children
- **ALL** → Apply everything

| Cascade Type | Description                                                                                                          | When It's Used                                                                   | Example Scenario                                                                   |
| ------------ | -------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------- |
| **PERSIST**  | Propagates the `persist()` operation to child entities. If the parent is saved, its new children are also persisted. | When creating new entities with relationships.                                   | Saving a new `Order` automatically saves its new `OrderItems`.                     |
| **MERGE**    | Propagates the `merge()` operation to child entities. Updates detached entities by merging changes.                  | When updating entities loaded in a different session (detached).                 | Merging an updated `Order` also updates its `OrderItems`.                          |
| **REMOVE**   | Propagates the `remove()` operation to child entities. Deleting the parent deletes the children.                     | When child’s lifecycle is strictly dependent on the parent. (Use carefully)      | Deleting an `Order` also deletes all its `OrderItems`.                             |
| **REFRESH**  | Propagates the `refresh()` operation and reloads child entities from the DB, discarding unsaved changes.             | To sync both parent and children with the latest DB state.                       | Refreshing an `Order` reloads its `OrderItems` from the database.                  |
| **DETACH**   | Propagates the `detach()` operation, removing children from the persistence context.                                 | When detaching objects for serialization or sending outside persistence context. | Detaching an `Order` also detaches its `OrderItems`.                               |
| **ALL**      | Applies **all** cascade operations (`PERSIST`, `MERGE`, `REMOVE`, `REFRESH`, `DETACH`).                              | When child life cycle is completely dependent on the parent.                     | An `Order` fully manages `OrderItems` for create, update, delete, refresh, detach. |

### ➡️ Best Practice Tips

| Scenario                                                                                      | Recommended Cascade        |
| --------------------------------------------------------------------------------------------- | -------------------------- |
| Parent owns and controls child lifecycle (OrderItems within an Order)                         | `CascadeType.ALL`          |
| Parent & child inserted together but deletion should not delete child (e.g., Student–Courses) | `PERSIST` & `MERGE` only   |
| Shared child referenced by multiple parents (e.g., Roles, Categories)                         | ❌ Do **NOT** use `REMOVE` |

### ➡️ Why can using CascadeType.ALL sometimes cause data loss?

- CascadeType.ALL means “whatever happens to the parent should also happen to the child.”
  - That includes: PERSIST, MERGE, REFRESH, DETACH, REMOVE ← this is the dangerous one
- When you blindly apply `CascadeType.ALL`, you unintentionally give Hibernate permission to delete child records whenever the parent is deleted OR whenever a parent-child relationship changes And that’s where data loss happens.

#### 🟦 Root Causes of Data Loss

##### 🔵 Unintended child deletion (REMOVE cascades down)

- If your association has:

```java
@OneToMany(mappedBy = "order", cascade = CascadeType.ALL)
private List<OrderItem> items;

```

- Then removing the parent:

```java
  entityManager.remove(order);

```

- will also remove ALL order items, If you didn’t intend to delete them → data loss.

##### 🔵 Removing a child from the collection triggers DELETE

##### 🔵 Replacing the collection clears the table

##### 🔵 Cascading REMOVE on relationships that should be shared

##### 🔵 Orphan Removal + Cascade ALL = double danger

- If you have:

```java
  @OneToMany(mappedBy="user", cascade=CascadeType.ALL, orphanRemoval=true)

```

- removing a child from a list:

```java
  user.getAddresses().clear();

```

deletes every address row from the database permanently.
Even if you just wanted to update the list → gone.

#### 🟦 Use it only when the child entity truly depends on the parent and should live + die WITH the parent.

- **Good use cases:**
  - Order → OrderItems
  - Blog → Comments
  - Invoice → LineItems

- **Bad use cases:**
  - User → Roles
  - User → Address (sometimes shared)
  - Employee → Department
  - Student → Courses

# ⏺️ Orphan Removal In JPA

- Orphan Removal = Automatically delete child entities that are no longer referenced by the parent.
- If the parent no longer ‘owns’ this child, JPA should delete it from the database

```java
  @OneToMany(mappedBy = "parent", orphanRemoval = true)
private List<Address> addresses;

```

➡️ In domain models, some child records shouldn't live without the parent.

- A User → UserProfile
- A Cart → CartItem
- A Question → Options
- An Order → OrderLineItems

If the parent removes reference, those should not remain “dangling” in the DB.
