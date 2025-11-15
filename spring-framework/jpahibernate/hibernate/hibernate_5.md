⏺️ ➡️ 🟦 🔵 🟢🔴⭕🟠🟣🟥🟧✔️ ☑️ • ‣ → ⁕

# ⏺️ Dirty Checking

Dirty checking in Hibernate is the automated process by which Hibernate detects changes made to managed (persistent) entities by comparing their current in-memory state with the original snapshot stored in the persistence context. During the flush phase, if Hibernate finds any differences, it marks the entity as “dirty” and generates the necessary SQL UPDATE statements to synchronize the database with the modified state—without requiring explicit update calls from the developer.

## ➡️ How Hibernate Dirty Checking Works Internally (Under the Hood)

- Hibernate performs dirty checking inside the Persistence Context (a.k.a. first-level cache).
- Whenever you load an entity, Hibernate keeps two copies

### 🟦 Snapshot (original state)

- A deep copy of the entity when it was first loaded.
- Stored internally in the persistence context.

### 🟦 Managed entity (current state)

- The actual Java object you hold and modify.
- Hibernate compares these two during flushing.

## ➡️ Step-by-Step Internal Mechanism

#### 🟦 Entity is Loaded

- It stores a snapshot of its values in a map (usually `EntityEntry` inside **PersistenceContext**).
- The entity is put in Managed state.

```java
  original snapshot (name="John", salary=5000)
  managed entity (same fields)

```

#### 🟦 You Modify the Entity

- Only the managed entity changes.
- The snapshot does NOT change.

```java
   user.setSalary(6000);

```

#### 🟦 On Flush → Hibernate triggers dirty checking

- Hibernate flush happens automatically:

  - before query execution,
  - before transaction commit,
  - or manually by calling entityManager.flush().

- At flush time:

  - Hibernate compares:
    Hibernate internally calls: **Interceptor#onFlushDirty()** OR **EntityPersister#findDirty()**

- These methods:

  - iterate over each property,
  - compare old vs new using property Type classes,
  - detect differences using `isDirty()`.

#### 🟦 If fields changed → Hibernate marks entity as dirty

- It prepares an **UPDATE SQL** like:

```java
  update users set salary = ? where id = ?
```

Hibernate only includes changed columns, not all columns
(unless `dynamic-update = false`).

#### 🟦 Snapshot is updated

- After flushing:

  - Hibernate updates the snapshot with the new values.
  - So next time it compares consistently.

  ## ➡️ Do developers perform dirty checking manually in Spring Boot?

As developers, we only modify the entity — Hibernate detects changes and syncs them to the database during flush.

- When an entity is in the Persistent/Managed state (inside the Persistence Context):

  - You modify the entity in Java code
  - `user.setSalary(6000);`

- You do NOT call update(), merge(), or any SQL.
- On flush/commit, Hibernate:

  - automatically compares the current entity state ↔︎ the original snapshot and generates an UPDATE only if needed.

- This behavior is built-in. You, as a developer, simply change the object.
