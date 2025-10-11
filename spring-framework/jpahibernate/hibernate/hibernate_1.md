🔵🟢🔴➡️⭕🟠🟦🟣🟥🟧✔️⏺️ ☑️ • ‣ → ⁕

# ⏺️

- Hibernate is a powerful, high-performance Object-Relational Mapping (ORM) framework for Java.
- It provides a way to map Java classes (objects) to database tables and automate the persistence of objects in relational databases.
- Hibernate acts as a JPA Provider — meaning it implements the JPA specification and provides the actual working code.

## ➡️ Key Goals of Hibernate:

- Eliminate boilerplate JDBC code.
- Simplify database interactions through object mapping.
- Handle SQL generation and execution internally.
- Provide caching, lazy loading, transaction management, and querying via HQL/Criteria.

## ➡️ Architecture of Hibernate

- Hibernate’s architecture consists of multiple layers and components:

```java
Application
   |
   v
Session (org.hibernate.Session)
   |
Transaction (org.hibernate.Transaction)
   |
SessionFactory (org.hibernate.SessionFactory)
   |
JDBC / Database

```

## ➡️ Key Components

### 🟦 Configuration

Used to configure Hibernate and build `SessionFactory`. Reads `hibernate.cfg.xml` or annotations.

### 🟦 SessionFactory

A heavyweight object created once per application. It creates `Session` objects.

### 🟦 Session

- It acts as a bridge between your Java application and the database.
- A lightweight, short-lived object that represents a single unit of work with the DB.
- `org.hibernate.Session` is the primary interface for performing CRUD operations in Hibernate.
- A Session represents a single unit of work (often one request/transaction).
- It’s not thread-safe, so it should not be shared between threads.

#### 🔵 Responsibilities of Session

- Managing the Persistent Context (also called First-Level Cache)
- Performing CRUD operations on entities
- Handling transactions (with help of Transaction object)
- Dirty checking (auto-updating changed entities before commit)
- Executing HQL/JPQL/Native queries

```java
Session session = sessionFactory.openSession();
Transaction tx = session.beginTransaction();

User user = new User();
user.setName("Gulam");
session.save(user);

tx.commit();
session.close();

```

#### 🔵 Session Methods

##### save(Object entity)

- **Purpose:** Persist a transient entity to the database.
- **Returns:** The generated identifier (Serializable).
- **Effect:** Inserts a new record.

```java
Session session = sessionFactory.openSession();
Transaction tx = session.beginTransaction();

User user = new User();
user.setName("John");
session.save(user);

tx.commit();
session.close();

```

- The object moves from Transient → Persistent state.

#### persist(Object entity)

- **Purpose:** Makes a transient instance persistent.
- **Return:** void (unlike save which returns ID).
- **JPA standard method** (Hibernate supports it too).
- **Effect:** Insert occurs only when transaction is committed or flush is called.

```java
session.persist(user);

```

- **persist()** is preferred in JPA-compliant code.
- **save()** is a Hibernate-specific method.

#### get(Class<T> entityClass, Serializable id)

- **Purpose:** Fetch entity by primary key.
- **Behavior:** Hits the database immediately.
- **Return:** Entity object or null if not found.

```java
User u = session.get(User.class, 1L);

```

- Safe to use when you expect the entity might not exist.

#### load(Class<T> entityClass, Serializable id)

- **Purpose:** Returns a proxy object without hitting the database immediately.
- **Behavior:** Actual DB query occurs only when a method on the object is accessed.
- **Exception:** Throws ObjectNotFoundException if no row found when proxy is initialized.

```java
User u = session.load(User.class, 1L);

```

- Use **load()** when you are sure the entity exists, for **better performance (lazy loading)🔴**.

#### update(Object entity)

- **Purpose:** Update the entity state in the database.
- **Use Case:** When you have a detached entity and want to reattach it to the session.

```java
User user = session.get(User.class, 1L);
session.evict(user); // detached
user.setName("Updated");
session.update(user); // reattach and update

```

#### merge(Object entity)

- **Purpose:** Merge changes from a detached entity into the persistent context.
- **Return:** The managed instance.
- **Difference from update:** merge copies values, while update reattaches the same object.

```java
User detachedUser = ... // from previous session
detachedUser.setName("New Name");

User managed = (User) session.merge(detachedUser);

```

- erge() is safer in detached scenarios, especially with multiple sessions.

#### delete(Object entity)

- **Purpose:** Delete the entity from the database.
- **Effect:** Marks the entity for removal, actual delete occurs on commit or flush.

```java
User user = session.get(User.class, 1L);
session.delete(user);

```

#### flush()

- **Purpose:** Synchronize session state with the database.
- **Effect:** Executes SQL statements without committing the transaction

```java
session.flush();

```

- Typically used when you want to force Hibernate to execute pending SQL early.

#### clear()

- **Purpose:** Clear the persistence context, detaching all entities.
- **Use Case:** Useful for large batch operations to avoid memory bloat.

```java
session.clear();

```

#### evict(Object entity)

- **Purpose:** Remove a specific entity from the session cache (detach it).
- **Use Case:** If you don’t want Hibernate to track this object anymore.

```java
 session.evict(user);

```

#### close()

- **Purpose:** Close the session and release database connections.

```java
session.close();

```

### 🟦 Transaction

Ensures atomic operations — commit or rollback changes.

```java

```

### 🟦 Query / Criteria

- Used to execute HQL, SQL, or Criteria API queries.

```java

```

#### 🟦 Entity Classes

Your Java POJOs mapped to database tables using annotations or XML.

```java

```

## ➡️ Hibernate Query Methods

- Hibernate provides multiple ways to run queries:

### 🟦 HQL (Hibernate Query Language)

- Object-oriented query language similar to SQL.
- Operates on entity objects, not tables.

```java
Query query = session.createQuery("FROM User WHERE name = :name");
query.setParameter("name", "John");
List<User> users = query.list();

```

### 🟦 Criteria API (Type-safe)

```java
 CriteriaBuilder cb = session.getCriteriaBuilder();
CriteriaQuery<User> cq = cb.createQuery(User.class);
Root<User> root = cq.from(User.class);
cq.select(root).where(cb.equal(root.get("name"), "John"));

List<User> results = session.createQuery(cq).getResultList();

```

### 🟦 Native SQL Queries

```java
 SQLQuery query = session.createSQLQuery("SELECT * FROM users WHERE name = :name");
query.addEntity(User.class);
query.setParameter("name", "John");
List<User> users = query.list();

```
