# Annotations help in object-relational mapping (ORM) are:

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


