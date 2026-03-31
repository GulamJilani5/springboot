🔵🟢🔴➡️⭕🟠🟦🟣🟥🟧✔️⏺️ ☑️ • ‣ → ⁕

# ⏺️ Pagination

- Pagination is a technique used to divide a large result set into smaller, manageable chunks (pages) to improve performance and user experience.
- Fetch and display data incrementally rather than loading everything at once. This improves performance and reduces memory usage.
- **Page Size:** Number of records per page (e.g., 10 items per page).
- **Page Number:** The current page index (starting from 0).
- **Offset:** Calculated as pageNumber \* pageSize to skip records.
- **Total Pages/Elements:** Derived from the total count of matching records
- **Page Request:** A request object specifying the page number and size.

### 🟦 Repository:

```java

import org.springframework.data.domain.Page;
import org.springframework.data.domain.Pageable;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.data.jpa.repository.Query;

public interface UserRepository extends JpaRepository<User, Long> {
    // Paginated query with sorting (combines both concepts)
    Page<User> findByAgeGreaterThan(int age, Pageable pageable);

    // Custom JPQL with pagination
    @Query("SELECT u FROM User u WHERE u.status = 'ACTIVE'")
    Page<User> findActiveUsers(Pageable pageable);
}
```

### 🟦 Service Layer

```java
import org.springframework.data.domain.PageRequest;
import org.springframework.data.domain.Pageable;
import org.springframework.data.domain.Sort;

@Service
public class UserService {
    @Autowired
    private UserRepository userRepository;

    public Page<User> getUsers(int page, int size) {
        Pageable pageable = PageRequest.of(page, size);  // Page 0, size 10
        return userRepository.findByAgeGreaterThan(18, pageable);
    }

    public Page<User> getSortedPaginatedUsers(int page, int size, String sortBy, Sort.Direction direction) {
        Sort sort = Sort.by(direction, sortBy);  // e.g., Sort.by(Sort.Direction.ASC, "name")
        Pageable pageable = PageRequest.of(page, size, sort);
        return userRepository.findByAgeGreaterThan(18, pageable);
    }

    // Example addition to UserService
    public Page<User> getActiveUsers(int page, int size) {
        Pageable pageable = PageRequest.of(page, size);
        return userRepository.findActiveUsers(pageable);
    }
}
```

### Controller

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.domain.Page;
import org.springframework.data.domain.Sort;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import java.util.HashMap;
import java.util.Map;

@RestController
@RequestMapping("/api/users")
public class UserController {

    @Autowired
    private UserService userService;

    // Basic pagination: GET /api/users?page=0&size=10
    @GetMapping
    public ResponseEntity<Page<User>> getUsers(
            @RequestParam(defaultValue = "0") int page,
            @RequestParam(defaultValue = "10") int size) {
        Page<User> users = userService.getUsers(page, size);
        return ResponseEntity.ok(users);
    }

    // Sorted pagination: GET /api/users/sorted?page=0&size=10&sortBy=name&direction=ASC
    @GetMapping("/sorted")
    public ResponseEntity<Page<User>> getSortedPaginatedUsers(
            @RequestParam(defaultValue = "0") int page,
            @RequestParam(defaultValue = "10") int size,
            @RequestParam(defaultValue = "name") String sortBy,
            @RequestParam(defaultValue = "ASC") Sort.Direction direction) {
        Page<User> users = userService.getSortedPaginatedUsers(page, size, sortBy, direction);
        return ResponseEntity.ok(users);
    }

    // Active users pagination: GET /api/users/active?page=0&size=10
    // (Assumes you've added the getActiveUsers() method to UserService as suggested above)
    @GetMapping("/active")
    public ResponseEntity<Page<User>> getActiveUsers(
            @RequestParam(defaultValue = "0") int page,
            @RequestParam(defaultValue = "10") int size) {
        Page<User> users = userService.getActiveUsers(page, size);
        return ResponseEntity.ok(users);
    }

    // Optional: Custom response wrapper for metadata (e.g., total pages, etc.)
    // This could be returned instead of raw Page<User> for a more API-friendly structure
    @GetMapping("/metadata")
    public ResponseEntity<Map<String, Object>> getUsersWithMetadata(
            @RequestParam(defaultValue = "0") int page,
            @RequestParam(defaultValue = "10") int size) {
        Page<User> users = userService.getUsers(page, size);
        Map<String, Object> response = new HashMap<>();
        response.put("content", users.getContent());
        response.put("page", users.getNumber());
        response.put("size", users.getSize());
        response.put("totalElements", users.getTotalElements());
        response.put("totalPages", users.getTotalPages());
        response.put("last", users.isLast());
        return ResponseEntity.ok(response);
    }
}
```

- **Result:** Returns a Page<User> object containing:
  - **content:** List of entities on the current page.
  - **totalElements:** Total count of matching records.
  - **totalPages:** Total number of pages.
  - **number:** Current page number.

# ⏺️ Sorting

- Sorting orders the result set based on one or more properties (fields) in ascending (ASC) or descending (DESC) order.
- **Single Sort:** Order by one field (e.g., by name ASC).
- **Multi-Sort:** Order by multiple fields (e.g., by age DESC, then name ASC).
- **Dynamic Sorting:** User-driven, often passed as parameters.

### 🟦 Repository

```java
    public interface UserRepository extends JpaRepository<User, Long> {
        // Built-in sorting via method name (e.g., findByAgeGreaterThanOrderByNameAsc)
        List<User> findByAgeGreaterThan(int age, Sort sort);

        // Or use Pageable for combined pagination + sorting
        Page<User> findByAgeGreaterThan(int age, Pageable pageable);
    }
```

### 🟦 Service

```java
    public List<User> getSortedUsers(String sortBy, Sort.Direction direction) {
        Sort sort = Sort.by(direction, sortBy);  // e.g., Sort.by(Sort.Direction.DESC, "age")
        return userRepository.findByAgeGreaterThan(18, sort);
    }

    // Multi-sort
    public List<User> getMultiSortedUsers() {
        Sort sort = Sort.by(Sort.Direction.DESC, "age").and(Sort.by(Sort.Direction.ASC, "name"));
        return userRepository.findByAgeGreaterThan(18, sort);
    }
```

### 🟦 Controller

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.domain.Sort;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;
import org.springframework.web.server.ResponseStatusException;

import java.util.List;

@RestController
@RequestMapping("/api/users")
public class UserController {

    @Autowired
    private UserService userService;

    // Dynamic sorting: GET /api/users/sorted?sortBy=age&direction=DESC
    @GetMapping("/sorted")
    public ResponseEntity<List<User>> getSortedUsers(
            @RequestParam(defaultValue = "name") String sortBy,
            @RequestParam(defaultValue = "ASC") String directionStr) {
        try {
            Sort.Direction direction = Sort.Direction.fromString(directionStr.toUpperCase());
            List<User> users = userService.getSortedUsers(sortBy, direction);
            return ResponseEntity.ok(users);
        } catch (IllegalArgumentException e) {
            throw new ResponseStatusException(HttpStatus.BAD_REQUEST, "Invalid direction: " + directionStr + ". Use ASC or DESC.");
        }
    }

    // Multi-sorting: GET /api/users/multi-sorted
    @GetMapping("/multi-sorted")
    public ResponseEntity<List<User>> getMultiSortedUsers() {
        List<User> users = userService.getMultiSortedUsers();
        return ResponseEntity.ok(users);
    }
}
```
