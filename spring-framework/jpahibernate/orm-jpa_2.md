🔵🟢🔴➡️⭕🟠🟦🟣🟥🟧✔️⏺️ ☑️ • ‣ → ⁕

# ⏺️ Entity States

| Hibernate State | Equivalent JPA State |
| --------------- | -------------------- |
| **Transient**   | **New**              |
| **Persistent**  | **Managed**          |
| **Detached**    | **Detached**         |
| **Removed**     | **Removed**          |

- In JPA, states are similar but termed "**new**," "**managed**," "**detached**," "**removed**."

## ➡️ Transient/New

- A new entity instance not yet associated with a session. No database representation; changes aren't tracked.
- Garbage-collectable if unreferenced.
- The entity has just been instantiated via new, and is not associated with any EntityManager. It doesn’t have a database identity.
- **Transition:** Becomes persistent via save()/persist().

```java
User user = new User("Transient User"); // Transient, no ID, not in DB
// Changes to user won't be saved until persisted
```

## ➡️ Persistent / Managed

- The entity is currently associated with an open persistence context (**EntityManager**) and its changes will be automatically synchronized with the database at flush/commit.
- Entity is associated with an active session, has a database ID, and changes are automatically tracked (dirty checking) and synced on **flush/commit**. In the first-level cache.
- **Transition:** From **transient** via `save()/persist()`; from **detached** via `update()/merge()`.

```java
Session session = sessionFactory.openSession();
Transaction tx = session.beginTransaction();
User user = new User("New User");
session.save(user); // Now persistent, ID generated
user.setName("Updated"); // Change auto-detected and updated on commit
tx.commit(); // INSERT then UPDATE if needed
session.close();
```

## ➡️ Detached

- The entity was managed before, but is no longer associated with any persistence context (e.g., after closing the **EntityManager**). Changes to it are not tracked anymore.
- Was persistent but the session is closed. Has an ID and DB row, but changes aren't tracked. Can be reattached.
- **Transition:** When session closes or entity is evicted (`session.evict()`). Reattach via `update()/merge()/saveOrUpdate()`.

```java
// After above commit and close, user is detached
user.setName("Detached Update"); // Change not auto-saved
Session newSession = sessionFactory.openSession();
Transaction tx2 = newSession.beginTransaction();
newSession.update(user); // Reattaches, becomes persistent, update synced
tx2.commit();
```

## ➡️ Removed

- The entity has been marked for deletion via `entityManager.remove(entity)` and will be deleted when the transaction is committed.
- Marked for deletion in the current session. Still in cache until commit, when **DELETE** is executed. Accessing it after removal may throw exceptions.
- Transition: Via `session.delete()/em.remove()`. After commit, it's transient-like but should not be reused.

```java
Session session = sessionFactory.openSession();
Transaction tx = session.beginTransaction();
User user = session.get(User.class, 1L); // Persistent
session.delete(user); // Now removed, DELETE on commit
tx.commit(); // Executes DELETE
session.close();
// user is gone from DB, treat as transient
```
