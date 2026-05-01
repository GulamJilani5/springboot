⏺️ ➡️ 🟦 🟩 🟢 🔵 🔷 🔹🔴 ☑️ ✔️ ✓→•←⁕⁂※⁜‣

# ⏺️ Solution 1: Fetch Joins with JPQL or Spring Data JPA

- Use a JPQL query with a JOIN FETCH to explicitly fetch posts and comments in one query.
- Create a **Custom Query** in **Repository:**

##### 🔵 Comment (Owning Side)

```java
import jakarta.persistence.*;

@Entity
public class Comment {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String review;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "post_id") // 🔥 FK lives here
    private Post post;

    // getters & setters
}
```

##### 🔵 Post (Inverse Side)

```java
import jakarta.persistence.*;
import java.util.ArrayList;
import java.util.List;

@Entity
public class Post {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String title;

    @OneToMany(mappedBy = "post", cascade = CascadeType.ALL, orphanRemoval = true)
    private List<Comment> comments = new ArrayList<>();

    // 🔥 Helper method (VERY IMPORTANT)
    public void addComment(Comment comment) {
        comments.add(comment);
        comment.setPost(this);
    }

    public void removeComment(Comment comment) {
        comments.remove(comment);
        comment.setPost(null);
    }

    // getters & setters
}
```

##### 🔵 Repository

```java
@Repository
public interface PostRepository extends JpaRepository<Post, Long> {

    @Query("SELECT DISTINCT p FROM Post p LEFT JOIN FETCH p.comments")
    List<Post> findAllWithComments();

    // SQL DISTINCT ≠ JPA DISTINCT, Hibernate handles deduplication in memory so Hibernate may still return duplicates internally before filtering.
    // So Use below instead of above
    @Query("SELECT p FROM Post p LEFT JOIN FETCH p.comments")
    @Transactional(readOnly = true)
    List<Post> findAllWithComments();
    // spring.jpa.properties.hibernate.query.passDistinctThrough=false

}
```

```sql
 @Query("SELECT p FROM Post p LEFT JOIN FETCH p.comments")
```

- Translated to:

```sql
SELECT p.*, c.*
FROM post p
LEFT JOIN comment c
ON p.id = c.post_id;
```

##### 🔵 Service

```java
@Service
public class PostService {

   public PostService(PostRepository postRepository) {
        this.postRepository = postRepository;
    }

    public List<Post> getAllPostsWithComments() {
        List<Post> posts = postRepository.findAllWithComments();

        for (Post post : posts) {
            System.out.println(post.getComments().size()); // No extra queries
        }

        return posts;
    }
}
```

### 🟦 What Happens

- **JOIN FETCH** tells Hibernate to fetch posts and their comments in one query.
- Example SQL:

```sql
SELECT p._, c._
FROM Post p
LEFT JOIN Comment c
ON p.id = c.post_id
```

- Total: 1 query.

### 🟦 Benefits

- Explicit control over what’s fetched.
- Works with FetchType.LAZY, so you don’t need to change the entity.

### 🟦 When to Use

- Best for specific endpoints where you need related data.
