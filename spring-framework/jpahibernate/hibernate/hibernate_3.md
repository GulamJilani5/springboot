🔵🟢🔴➡️⭕🟠🟦🟣🟥🟧✔️⏺️ ☑️ • ‣ → ⁕

# ⏺️ What is the N+1 Problem and how to resolve it

## ➡️ What is the N+1 Problem

The **N+1** problem is a common issue in software development, especially when working with databases and object-relational mapping (ORM) tools like those in SQL, **Hibernate**, Rails, or Entity Framework. It happens when your code accidentally makes way too many database queries (requests for data) instead of just a few efficient ones. This can slow down your app, waste resources, and make things laggy.

- **Why Does It Happen?**
  - **ORMs** often use "lazy loading" by default. This means they only load related data when you actually access it in your code.
  - It seems convenient at first, but if you're looping through a list of objects and accessing their relations inside the loop, it triggers a new query each time.

#### 🟦 Example Scenario

- Imagine a Spring Boot blog application with two entities:
  - **Post:** Represents a blog post.
  - **Comment:** Represents comments on a post. Each post can have multiple comments (a one-to-many relationship).
- **Entity**

```java
  @Entity
public class Post {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String title;

    @OneToMany(mappedBy = "post", fetch = FetchType.LAZY)
    private List<Comment> comments;

    // Getters and setters
}

@Entity
public class Comment {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String text;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "post_id")
    private Post post;

    // Getters and setters
}
```

- `@OneToMany` and `@ManyToOne` define the relationship, and `fetch = FetchType.LAZY` means related data (**e.g.**, comments for a post) is loaded only when accessed.
- Suppose we want to fetch all posts and display their comments. We might write this in a **Spring Data JPA** **repository** and a **service.**

- **repository**

```java
   @Repository
    public interface PostRepository extends JpaRepository<Post, Long> {
    }
```

- **service**

```java
  @Service
public class PostService {
    @Autowired
    private PostRepository postRepository;

    public List<Post> getAllPostsWithComments() {
        List<Post> posts = postRepository.findAll();
        for (Post post : posts) {
            // Accessing comments triggers a new query for each post
            System.out.println(post.getComments().size());
        }
        return posts;
    }
}
```

- **What Happens?**

  - **1 Query for Posts:**

    - `postRepository.findAll()` executes: SELECT \* FROM Post.
    - Let’s say it returns 10 posts.

  - **N Queries for Comments:**

    - When you call `post.getComments()` in the loop, **Hibernate** lazily loads the comments for each post.
    - For each of the 10 posts, Hibernate runs a query like: `SELECT * FROM Comment WHERE post_id = ?`.
    - That’s 10 additional queries.

  - **Total Queries:**
    - 1 (for posts) + 10 (one per post for comments) = 11 queries.
    - If you had 100 posts, it would be 101 queries. This is the N+1 problem.

- **Why Is This Bad?**
  - **Performance Impact:** Each query involves network round-trips to the database, which is slow.
  - **Scalability Issues:** With more posts, the number of queries grows linearly, causing delays.
  - **Hidden in Development:** It might seem fine with small test data but fails with production-scale data.

## ➡️ Solutions to the N+1 Problem in Spring Boot

The goal is to reduce the number of queries, ideally to 1 or 2, by fetching all required data upfront. Here are the main solutions, explained step by step.

### 🟦 Solution 1: Eager Loading with FetchType.EAGER

- Change the fetch strategy to load related data immediately.

```java
  @Entity
  public class Post {
        @Id
        @GeneratedValue(strategy = GenerationType.IDENTITY)
        private Long id;
        private String title;

        @OneToMany(mappedBy = "post", fetch = FetchType.EAGER)
        private List<Comment> comments;
        // Getters and setters
  }
```

- **What Happens:**

  - When `postRepository.findAll()` runs, **Hibernate** fetches all posts and their comments in one go,  
     typically using a `JOIN`.
  - Query example: `SELECT p._, c._ FROM Post p LEFT JOIN Comment c ON p.id = c.post_id`.
  - Total: 1 query (or sometimes 2, depending on the database).

- **Downside:**

  - Eager loading fetches comments even if you don’t need them, wasting resources.
  - Not ideal for all cases, as it can load too much data unnecessarily.

- **When to Use:**
  - Small datasets or when you always need the related data. Like comments with the Post data.

### 🟦 Solution 2: Fetch Joins with JPQL or Spring Data JPA

- Use a JPQL query with a JOIN FETCH to explicitly fetch posts and comments in one query.
- Create a **Custom Query** in **Repository:**

```java
   @Repository
   public interface PostRepository extends JpaRepository<Post, Long> {
        @Query("SELECT p FROM Post p LEFT JOIN FETCH p.comments")
        List<Post> findAllWithComments();
   }
```

- Update Service:

```java
    @Service
    public class PostService {
        @Autowired
        private PostRepository postRepository;

        public List<Post> getAllPostsWithComments() {
            List<Post> posts = postRepository.findAllWithComments();
            for (Post post : posts) {
                System.out.println(post.getComments().size()); // No extra queries!
            }
            return posts;
        }
    }
```

- **What Happens:**

  - JOIN FETCH tells Hibernate to fetch posts and their comments in one query.
    Example SQL: SELECT p._, c._ FROM Post p LEFT JOIN Comment c ON p.id = c.post_id.
    Total: 1 query.

- **Benefits:**

  - Explicit control over what’s fetched.
  - Works with FetchType.LAZY, so you don’t need to change the entity.

- **When to Use:**
  - Best for specific endpoints where you need related data.

### 🟦 Solution 3: Entity Graph

- Spring Data JPA’s `@EntityGraph` lets you define which relationships to fetch eagerly, overriding the default `FetchType.LAZY`.
- Add **Entity Graph** to **Repository**:

```java
   @Repository
    public interface PostRepository extends JpaRepository<Post, Long> {
        @EntityGraph(attributePaths = {"comments"})
        List<Post> findAll();
    }
```

- What Happens:

  - **@EntityGraph(attributePaths = {"comments"})** tells Hibernate to fetch the comments collection along with posts.
  - Similar to `JOIN FETCH`, it results in 1 query.

- Alternative with Named **Entity Graph:**

  - Define a reusable graph in the entity:

  ```java
     @Entity
    @NamedEntityGraph(
        name = "Post.comments",
        attributeNodes = @NamedAttributeNode("comments")
    )
    public class Post { ... }
  ```

  - Then use it in the repository:

  ```java
   @EntityGraph(value = "Post.comments")
   List<Post> findAll();
  ```

- Benefits:
  - Cleaner and reusable compared to JPQL.
  - Flexible for different fetch scenarios.
- When to Use:
  - When you want a declarative, reusable way to avoid N+1.

### 🟦 Solution 4: Batch Fetching

Hibernate can batch related data queries to reduce the number of queries.

- Configure Batch Size in Entity: Add `@BatchSize` to the comments collection:

```java
    @OneToMany(mappedBy = "post", fetch = FetchType.LAZY)
    @BatchSize(size = 100)
    private List<Comment> comments;
```

- **What Happens:**

  - Instead of 1 query per post, Hibernate fetches comments for multiple posts in batches.
  - Example: For 100 posts, it might run 1 query for posts + 1 query for comments of 100 posts (using IN clause: `SELECT \* FROM Comment WHERE post_id IN (?, ?, ?, ...)`).
  - Total: 2 queries instead of 101.

- **Benefits:**

  - Works with lazy loading, so no need to change fetch type.
  - Good middle ground for large datasets.

- **When to Use:**
  - When you can’t use eager loading or JOINs due to complex queries.

### 🟦 Solution 5: Custom SQL Query (Last Resort)

- If JPA isn’t flexible enough, write a raw **SQL query** and map the results.

- Write a Native Query:

```java
   @Repository
    public interface PostRepository extends JpaRepository<Post, Long> {
        @Query(value = "SELECT p.*, c.* FROM Post p LEFT JOIN Comment c ON p.id = c.post_id", nativeQuery = true)
        List<Object[]> findAllPostsWithCommentsNative();
    }
```

- Map Results in Service:

```java
  public List<Post> getAllPostsWithComments() {
    List<Object[]> results = postRepository.findAllPostsWithCommentsNative();
    // Custom logic to map results to Post and Comment objects
    // (You may need to group comments by post manually)
  }
```

- **Benefits:**

  - Full control over the query.
  - Can optimize for very specific cases.

- **Downsides:**

  - Loses JPA’s automatic mapping.
  - More manual work to handle results.

- **When to Use:**
  - Complex queries where JPA’s features fall short.

## ➡️ Additional Tips to Avoid N+1 in Spring Boot

### Enable Query Logging:

- Check logs to spot N+1 issues early
- application.properties

```java
   spring.jpa.show-sql=true
    spring.jpa.properties.hibernate.format_sql=true
    logging.level.org.hibernate.SQL=DEBUG
    logging.level.org.hibernate.type.descriptor.sql.BasicBinder=TRACE
```

- **Use Tools:**
  - Libraries like **Spring Data JPA N+1 Detector** or **Hibernate Statistics** can warn you about excessive queries.
