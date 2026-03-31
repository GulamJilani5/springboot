🔵🟢🔴➡️⭕🟠🟦🟣🟥🟧✔️⏺️ ☑️ • ‣ → ⁕

# Roadmap for learning ORM, JPA, Hibernate and Spring data JPA

### ➡️ Core JPA & ORM concepts (must understand deeply)

- **ORM idea:** map classes/objects → tables/rows and manage object identity, relationships, and lifecycle.
- **JPA vs Hibernate:** JPA = specification (APIs & annotations). Hibernate = one popular implementation (adds extras).
- **Entity basics:** @Entity, @Table, @Id, @GeneratedValue. Entities need default constructor, getters/setters.
- **Entity states:** transient (new), persistent (managed by EntityManager), detached, removed. Know how persist(), - merge(), detach(), remove() behave.
- **Flush vs Commit:** flush() writes SQL to DB but transaction not committed; commit flushes and commits.
- **Transactions:** JTA/JDBC transactions; in Spring use @Transactional. Understand propagation and readOnly flags.

### ➡️ Mappings & relationships — hands-on essentials (JPA)

- @OneToOne, @OneToMany, @ManyToOne, @ManyToMany.
- mappedBy (pick owning side) and @JoinColumn / @JoinTable.
- fetch = FetchType.LAZY (default for collections) vs EAGER. Prefer lazy for collections.
- Cascading (CascadeType.PERSIST, MERGE, REMOVE, ALL) and orphanRemoval=true — know semantics.
- @Embeddable / @Embedded, @ElementCollection for simple value collections.
- @Version for optimistic locking.
- Inheritance strategies: SINGLE_TABLE, JOINED, TABLE_PER_CLASS.

### ➡️ Querying: JPQL, Criteria, native, projections(JPA)

- **JPQL (HQL):** object-oriented SQL: SELECT b FROM Book b WHERE b.title LIKE :t.
- **Criteria API:** type-safe, programmatic queries (useful for dynamic queries).
- **Native queries:** createNativeQuery(...) when needed.
- **Spring Data Projections:** interface or DTO projections; select new com.example.dto.UserDto(u.id, u.name) for DTO construction.
- **Paging & Sorting:** Pageable, Page<T>, Sort.

### ➡️ Spring Data JPA — the productivity layer

- JpaRepository<T, ID> gives CRUD, paging, sorting out of the box.
- Method name query derivation: findByLastNameAndAgeGreaterThan(...).
- @Query for custom JPQL/native queries.
- Custom repository implementations for complex logic.
- @EntityGraph or JOIN FETCH in @Query to avoid N+1.

### ➡️ Common pitfalls & debugging checklist (learn to recognize & fix)(JPA)

- **LazyInitializationException:** accessing lazy relation outside transaction. Fix: keep transactional boundary, fetch join, or DTO projection.
- **N+1 queries:** detect with SQL logs; fix with JOIN FETCH, @EntityGraph, or batch fetching.
- **Wrong cascade/orphanRemoval usage:** remove children unexpectedly — test behaviors.
- **Bad equals/hashCode:** avoid using generated id in equals before persistence; implement carefully.
- **Bulk updates bypass EntityManager cache:** use executeUpdate() with em.clear() afterwards.
- **Incorrect GenerationType choice:** IDENTITY disables JDBC batching for inserts; use SEQUENCE for batching in many DBs.
- **Unclosed transactions / leaks:** monitor connection pool and transaction logs.
- **Schema drift:** use Flyway/Liquibase for migrations, not `ddl-auto=update` in production.

### ➡️ Performance & scaling tips

- Enable batching: spring.jpa.properties.hibernate.jdbc.batch_size=50 and use SEQUENCE strategy for IDs where possible.
- Use DTO queries for read-heavy endpoints to avoid heavy entity graphs.
- Add proper DB indexes (use @Index on columns).
- Use EXPLAIN on slow queries.
- Consider second-level cache (EHCache / Infinispan) carefully (cache invalidation complexities).
- Monitor with SQL logs, metrics, and DB monitoring tools.

### ➡️ Advanced topics (what to explore later)

- Second-level & query caches.
- Custom Hibernate types and AttributeConverter.
- Event listeners & interceptors.
- Multi-tenancy patterns.
- Stored procedures and native optimized SQL.
- Mapping complex inheritance and polymorphic queries.
- QueryDSL / CriteriaBuilder advanced usage.
- Production readiness: connection pool tuning (Hikari), monitoring, migrations (Flyway), and backup/restore.
