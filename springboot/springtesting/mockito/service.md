⏺️ ➡️ 🟦 🔵 🟢🔴⭕🟠🟣🟥🟧✔️ ☑️ • ‣ → ⁕

# ⏺️ Service Layer Testing

- Business Logic Testing
  - Validate calculations, rules, validations
  - Exception handling
  - Conditional flows
  - Data transformation
- Mock Repository

## ➡️ Service class and Corresponding Service Test Class

### 🟦 Service Class

```java
 @Service
public class UserService {

    private final UserRepository userRepository;

    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }

    public User getUserById(Long id) {

        return userRepository.findById(id)
                .orElseThrow(() -> new RuntimeException("User not found"));
    }
}

```

### 🟦 Service Test Class

```java
@ExtendWith(MockitoExtension.class)
class UserServiceTest {

    @InjectMocks
    private UserService userService;

    @Mock
    private UserRepository userRepository;

    @Test
    void shouldReturnUser() {

        User user = new User(1L, "Gulam");

        Mockito.when(userRepository.findById(1L))
               .thenReturn(Optional.of(user));

        User result = userService.getUserById(1L);

        assertEquals("Gulam", result.getName());
    }
}

```
