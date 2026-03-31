🔵🟢🔴➡️⭕🟠🟦🟣🟥🟧✔️⏺️ ☑️ • ‣ → ⁕

# ⏺️ Why You Should Avoid Primitive Types in JPA Entities

In JPA/Spring Boot, using primitives (int, long, boolean) inside entities is almost always a bad idea.
Instead, you should use their wrapper equivalents:

- int → Integer
- long → Long
- boolean → Boolean

### ➡️ Primitives cannot store NULL (Database can)

### ➡️ Wrapper types support nullability

### ➡️ Avoid confusing default values

- Primitives auto-initialize:

  - int age = 0;
  - boolean active = false;

- If you receive 0 or false, you can’t know:
- Did DB actually store 0/false?
- Or did Hibernate insert default primitive values?

This causes logic bugs.

### ➡️ Hibernate works better with wrappers

- Hibernate can:

  - lazy-load
  - proxy objects
  - detect dirty fields

- But Hibernate cannot proxy primitive types, because they must always have a value.
- Wrappers are objects → Hibernate can manage them better.

### ➡️ Cleaner validation

Annotations like: `@NotNull, @Min, @Max` Only make sense when the field can actually be `NULL`.

Applying **@NotNull** on an `int` is meaningless, because it can never be null.
