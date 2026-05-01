⏺️ ➡️ 🟦 🔵 🟢🔴⭕🟠🟣🟥🟧✔️ ☑️ • ‣ → ⁕

# ⏺️ Persistence Context in JPA

- The Persistence Context is a first-level cache maintained by the **EntityManager** (or Hibernate Session) that tracks all managed entity instances and their states during a transaction.

### ➡️ Key Responsibilities

| Role                                       | Description                                                                                                                                                                            |
| ------------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **1️⃣ Manages Entity Lifecycle**            | It keeps track of entity states — <br>➡️ **Transient → Persistent → Detached → Removed**.                                                                                              |
| **2️⃣ Ensures Identity Guarantee**          | Within one persistence context, **only one instance** of an entity with a given primary key exists. <br>Example: Two `find(User.class, 1)` calls return the **same object** reference. |
| **3️⃣ Provides Automatic Dirty Checking**   | When a managed entity changes, JPA automatically detects changes and **updates the database** at commit time — no need to call `update()`.                                             |
| **4️⃣ Enables Caching (First-Level Cache)** | All entities fetched within the same persistence context are stored in memory — **no extra SQL** for repeated reads.                                                                   |
| **5️⃣ Delays Writes (Write-Behind)**        | SQL statements may be batched and executed **just before transaction commit** for efficiency.                                                                                          |
| **6️⃣ Supports Lazy Loading**               | Related entities are lazily loaded as long as the persistence context is open.                                                                                                         |
| **7️⃣ Synchronization with Database**       | `flush()` forces synchronization — pushes changes from memory (context) to DB.                                                                                                         |

### ➡️ Analogy

- Think of the **Persistence Context** as a "**tracking memory**" for JPA:
  - It knows all the entities you’ve loaded.
  - It tracks changes to them.
  - It writes back those changes when you commit.
