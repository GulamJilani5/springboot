⏺️ ➡️ 🟦 🟩 🟢 🔵 🔷 🔹🔴 ☑️ ✔️ ✓→•←⁕⁂※⁜‣

# ⏺️ Solution 2: Entity Graph

- Spring Data JPA’s `@EntityGraph` lets you define which relationships to fetch eagerly, overriding the default `FetchType.LAZY`.
- Add **Entity Graph** to **Repository**:

```java
   @Repository
    public interface PostRepository extends JpaRepository<Post, Long> {
        @EntityGraph(attributePaths = {"comments"})
        List<Post> findAll();
    }
```

### 🟦 What Happens:

- **@EntityGraph(attributePaths = {"comments"})** tells Hibernate to fetch the comments collection along with posts.
- Similar to `JOIN FETCH`, it results in 1 query.
- Alternative with Named **Entity Graph:**
- Define a reusable graph in the **Entity**:

##### 🔵 Entity

```java
   @Entity
  @NamedEntityGraph(
      name = "Post.comments",
      attributeNodes = @NamedAttributeNode("comments")
  )
  public class Post { ... }
```

- Then use it in the repository:

##### 🔵 Repository

```java
 @EntityGraph(value = "Post.comments")
 List<Post> findAll();
```

### 🟦 Benefits:

- Cleaner and reusable compared to JPQL.
- Flexible for different fetch scenarios.

### 🟦 When to Use:

- When you want a declarative, reusable way to avoid N+1.
