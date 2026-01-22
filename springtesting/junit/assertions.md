⏺️ ➡️ 🟦 🔵 🟢🔴⭕🟠🟣🟥🟧✔️ ☑️ • ‣ → ⁕

# ⏺️

- JUnit Jupiter (`org.junit.jupiter.api.Assertions`)

### ➡️ Basic JUnit Assertions (Foundation)

- Assertions means validations.

##### 🟦 assertEquals(expected, actual)

- Used for status codes, IDs, values, counts

```java
assertEquals(200, response.getStatusCodeValue());

```

##### 🟦 assertNotEquals(unexpected, actual)

```java
  assertNotEquals("ADMIN", user.getRole());

```

##### 🟦 assertTrue(condition)

```java
  assertTrue(user.isActive());

```

##### 🟦 assertFalse(condition)

```java
assertFalse(user.isBlocked());

```

##### 🟦 assertNull(object)

```java
assertNull(user.getDeletedAt());

```

##### 🟦 assertNotNull(object)

```java
assertNotNull(user.getId());

```

### ➡️ Exception Assertions(VERY IMPORTANT 🔥)

##### 🟦assertThrows()

- Used when business validation fails.

```java
assertThrows(RuntimeException.class, () -> {
    userService.getUserById(100L);
});

```

- Interview favorite
- Used in service layer tests

##### 🟦 assertDoesNotThrow()

```java
assertDoesNotThrow(() -> userService.createUser(userDto));

```

### ➡️ Collection Assertions

##### 🟦 assertIterableEquals()

```java
  assertIterableEquals(expectedList, actualList);

```

##### 🟦 assertEquals(size, list.size())

```java
 assertEquals(3, users.size());

```

##### 🟦 assertTrue(list.isEmpty())

```java
 assertTrue(users.isEmpty());

```

### ➡️ Object Property Assertions

##### 🟦 Entity Validation

```java
User user = userService.getUserById(1L);

assertEquals("john", user.getUsername());
assertEquals("john@mail.com", user.getEmail());
assertTrue(user.isActive());

```

- Very common in Service + Repository tests

### ➡️ Assertions for REST Controller Tests (MockMvc)

##### 🟦 Status Code Assertions

```java
mockMvc.perform(get("/users/1"))
       .andExpect(status().isOk());

```

##### 🟦 JSON Assertions (jsonPath)

```java
.andExpect(jsonPath("$.id").value(1))
.andExpect(jsonPath("$.username").value("john"))
.andExpect(jsonPath("$.active").value(true));

```

- MOST USED for controller testing
- Interview-critical

### ➡️ Assertions with ResponseEntity

```java
 ResponseEntity<UserDto> response = userController.getUser(1L);

assertEquals(HttpStatus.OK, response.getStatusCode());
assertNotNull(response.getBody());
assertEquals("john", response.getBody().getUsername());

```

### ➡️ Repository Layer Assertions (JPA)

```java
Optional<User> userOpt = userRepository.findById(1L);
assertTrue(userOpt.isPresent());
assertEquals("john", userOpt.get().getUsername());

```

### ➡️ Grouped Assertions (assertAll)

- Executes all assertions even if one fails.

```java
assertAll(
    () -> assertEquals("john", user.getUsername()),
    () -> assertEquals("john@mail.com", user.getEmail()),
    () -> assertTrue(user.isActive())
);

```

- Professional-level testing
- Interview bonus

### ➡️ Timeout Assertions (Rare but Useful)

```java
assertTimeout(Duration.ofSeconds(2), () -> {
    userService.getAllUsers();
});

```

### ➡️ Assertions from AssertJ (Spring Boot Favorite)

- AssertJ is a separate assertion library.
- It is NOT part of JUnit and NOT part of Mockito.
- Cleaner
- More expressive
- Very popular in real projects

```java
assertThat(user)
    .isNotNull()
    .hasFieldOrPropertyWithValue("username", "john")
    .hasFieldOrPropertyWithValue("active", true);

```

##### 🟦 For Collection

```java
assertThat(users)
    .hasSize(3)
    .extracting(User::getUsername)
    .contains("john");

```

##### 🟦

```java

```

### ➡️

```java

```
