🔵🟢🔴➡️⭕🟠🟦🟣🟥🟧✔️⏺️ ☑️ • ‣ → ⁕

# Data Handling Pattern - DTO, Mapper and ORM

# 📦 DTO (Data Transfer Object)

A DTO is a simple class used to transfer data between layers (like controller ↔ service). It does not contain any business logic or database mapping.

**🧰 Use Case:**

- Improves security (exposes only needed fields),
- Helps decouple internal domain model from API.
- Makes data serialization/deserialization easier.

# Mapper

- Mapper is a component, class, or function that converts data from one representation (or object) to another — usually between different layers of an application.
- A Mapper takes an input object (or data structure) and produces an output object in a different form, mapping fields from one to the other.
  **🧰 Use Case:**

### 1️⃣ DTO ↔ Entity Conversion

- Converting between:
- Database entity objects (e.g., UserEntity) ↔ Data Transfer Objects (e.g., UserDTO)
- Allows separating the persistence model from what is exposed in APIs.

### 2️⃣ API Model ↔ Domain Model

- When your REST API uses request/response models different from your internal domain objects.

### 3️⃣ Legacy System Integration

- Mapping data structures from old systems to new system formats.

### 4️⃣ Data Transformation

- When consuming third-party APIs: transforming their response format into your app’s internal models.

### 5️⃣ Presentation Layer Models

- Preparing view-specific objects by mapping from domain or DTO objects.

# 🛠️ ORM (Object-Relational Mapping)

ORM is a technique that allows you to map Java objects to database tables. In Java, this is done using libraries like Hibernate/JPA.

**🧪 Example:**
Using JPA annotations like @Entity, @Table, @Column, etc., lets the ORM framework handle SQL under the hood:

**🧰 Use Case:**
ORM eliminates boilerplate SQL code. It enables CRUD operations using Java methods, improves productivity, and supports lazy loading, cascading, etc.
