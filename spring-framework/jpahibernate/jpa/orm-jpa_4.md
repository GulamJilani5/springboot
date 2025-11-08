🔵🟢🔴➡️⭕🟠🟦🟣🟥🟧✔️⏺️ ☑️ • ‣ → ⁕

# ⏺️ Cascade in JPA

- In JPA/Hibernate, Cascade defines how operations performed on a parent entity should automatically be applied to its related (child) entities.
- **Example:**
  - If you delete a Customer, should all their Orders also be deleted automatically?
    Cascading helps define this behavior.
- **Cascade** = `Automatic Propagation of Entity State Changes to Associated Entities.`

- **Cascading** is specified using the `@Cascade` annotation (in Hibernate) or the cascade attribute in JPA relationship annotations like **@OneToMany** or **@ManyToOne** etc.
- Without cascading, operations on the parent won't affect children, potentially leading to errors like **EntityNotFoundException** or **orphaned records**.

➡️ Cascade Types in JPA

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

➡️ Best Practice Tips

| Scenario                                                                                      | Recommended Cascade        |
| --------------------------------------------------------------------------------------------- | -------------------------- |
| Parent owns and controls child lifecycle (OrderItems within an Order)                         | `CascadeType.ALL`          |
| Parent & child inserted together but deletion should not delete child (e.g., Student–Courses) | `PERSIST` & `MERGE` only   |
| Shared child referenced by multiple parents (e.g., Roles, Categories)                         | ❌ Do **NOT** use `REMOVE` |
