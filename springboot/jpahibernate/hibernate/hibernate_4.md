⏺️ ➡️ 🟦 🔵 🟢🔴⭕🟠🟣🟥🟧✔️ ☑️ • ‣ → ⁕

# ⏺️ Caching in Hibernate

- Caching in Hibernate helps improve performance by reducing the number of database queries.
- When Hibernate needs an entity, it first checks if it already has it in memory (cache).
- If found — it reuses it, avoiding a database hit.
- When loading an entity (e.g., via session.get() or entityManager.find()), Hibernate checks the caches in this order:
  - First-level cache (if the entity is already loaded in the current session).
  - Second-level cache (if enabled and the entity is cached globally).
  - Database (as a fallback, populating the caches afterward).

### ➡️ First-Level Cache (Session Cache)

- The first-level cache is enabled by default in Hibernate.
- It is associated with the Session object.
- It stores entities that are read or saved during a single session.

#### 🟦 How It Works

- **Scope and Lifetime:** Limited to one session/transaction. It is automatically cleared when the session closes (e.g., via session.close()) or when the transaction commits/rolls back.
- **Data Storage:** Stores actual Java entity instances that have been loaded, queried, or modified during the session. It acts as the persistence context, tracking entity states (e.g., transient, persistent, detached) and ensuring persistent identity—meaning the same database row always maps to the same Java object within the session.
  - **Key Behaviors:**
    - **Read Operations:** When you load an entity (e.g., `session.get(Entity.class, id)`), Hibernate first checks the first-level cache. If the entity is found, it returns the cached instance directly, avoiding a database hit. If not, it queries the database and caches the result.
    - **Write Operations:** Any modifications to cached entities are tracked via **dirty checking**. During flush (explicit or at commit), changes are synchronized to the database, and the cache is updated accordingly.
    - **Eviction:** Entities are evicted only at session end or via explicit methods like `session.evict(entity), session.close();`.

### ➡️ Second-Level Cache (SessionFactory Cache)

The second-level cache is optional and must be explicitly enabled. It is shared across all sessions created from the same SessionFactory (application-wide), making it suitable for read-heavy applications where data doesn't change frequently.

🟦 How It Works

- **Scope and Lifetime:** Global to the SessionFactory; persists across sessions and transactions until explicitly evicted or the application shuts down.
- **Data Storage:** Stores serialized (or copied) entity data, not live Java instances, using a pluggable cache provider (e.g., Ehcache, Infinispan, or Caffeine). Entities must be marked as cacheable.
- **Key Behaviors:**
  - **Read Operations:** After checking the first-level cache, Hibernate queries the second-level cache. If the entity is present, it's deserialized into a Java instance and returned (bypassing the database). If a miss occurs, the database is queried, and the result is cached for future use.
  - **Write Operations:** Updates to entities are propagated back to the second-level cache during flush. It supports concurrency strategies like READ_ONLY (for immutable data), READ_WRITE (for mutable data with locking), or NONSTRICT_READ_WRITE (for relaxed consistency).
  - **Eviction and Invalidation:** Configurable via regions (e.g., per-entity caches) with policies for time-to-live (TTL), size limits, or manual eviction (e.g., `sessionFactory.getCache().evict(Entity.class)`). Query results can also be cached separately.
  - **Integration:** Works in tandem with the first-level cache—changes in one session can invalidate/update the shared second-level cache to maintain consistency.
