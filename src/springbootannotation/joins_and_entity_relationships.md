# ORM's Annotations

| Annotation        | Purpose                                      | Use With                |
|------------------|----------------------------------------------|-------------------------|
| @OneToOne        | One-to-one relationship                      | Entities                |
| @OneToMany       | One-to-many relationship                     | Parent entity           |
| @ManyToOne       | Many-to-one relationship                     | Child entity            |
| @ManyToMany      | Many-to-many relationship                    | Entities                |
| @JoinColumn      | Defines foreign key column                   | @OneToOne, @ManyToOne   |
| @JoinTable       | Defines custom join table                    | @ManyToMany             |
| @MappedBy        | Inverse side of relationship                 | @OneToMany, @ManyToMany |
| @Embedded        | Embeds a value-type object                   | Entity                  |
| @Embeddable      | Marks a class as embeddable                  | Embedded object         |


### ➡️@JoinTable — Used to Define a Join Table
- Typically used on the owning side of a `@ManyToMany` or `@OneToMany` relationship.
- To customize the name of the join table and columns.
- Control foreign key column names and constraints.

         @Entity
         public class Student {
         @Id
         private Long id;
            @ManyToMany
            @JoinTable(
                name = "student_course", // Name of the join table
                joinColumns = @JoinColumn(name = "student_id"), // FK to Student
                inverseJoinColumns = @JoinColumn(name = "course_id") // FK to Course
            )
            private List<Course> courses;
        }

| Mapping Type  | Supports `@JoinTable`? | Common Use Case                                                    |
| ------------- | ---------------------- | ------------------------------------------------------------------ |
| `@ManyToMany` | ✅ **Yes**              | Required to create join table between entities.                    |
| `@OneToMany`  | ✅ *Yes, but rare*      | Optional – used when no foreign key is stored in the child entity. |
| `@OneToOne`   | ✅ *Yes, but rare*      | Can be used if you want a join table (unusual).                    |
| `@ManyToOne`  | ❌ **No**               | Does **not** support `@JoinTable`. Use `@JoinColumn`.              |


💡 **Why @JoinTable Doesn’t Work with @ManyToOne**
- @ManyToOne always means the foreign key is in the current entity’s table, so we use @JoinColumn, 
  not a separate join table.

### ➡️mappedBy — Inverse Side of the Relationship
- **mappedBy** tells JPA that the current entity is not the owner of the relationship. The ownership lies with
the other side (where `@JoinColumn` or `@JoinTable` is defined).
- Typically on the inverse (non-owning) side of a bidirectional relationship.
- Used in @OneToMany, @ManyToMany, or @OneToOne.
- Prevents JPA from creating an unnecessary extra join table or column.
- Keeps bidirectional mapping in sync.

      @Entity
      public class Course {
      @Id
      private Long id;
    
      @ManyToMany(mappedBy = "courses") // points to 'courses' in Student
      private List<Student> students;
      }

**Taking both the example from the `@joinTable` and `@mappedBy`**
- Student is the owning side (uses `@JoinTable`).
- Course is the inverse side (uses `mappedBy`).
  
**Owning Side**: The owning side is the one that defines the join column or join table, and hence 
                  controls the relationship in the database (i.e., manages the foreign key).
**Inverse Side**: The inverse side(the one using mappedBy) is simply mapped to the owning side and does not
                  manage the foreign key column.

### 🔁 Real-world Analogy
- `@JoinTable` = "I’ll create the connection between A and B."
- `mappedBy` = "A already has the connection, I’ll just point to it."





