⏺️ ➡️ 🟦 🔵 🟢🔴⭕🟠🟣🟥🟧✔️ ☑️ • ‣ → ⁕

# ⏺️ Controller Layer Testing

- REST API test
  - REST endpoints
  - Validate request → response
  - Ensure HTTP status, JSON response, headers
- Mock User Repository
- Repository is NOT used directly in controller tests
- No real DB, no real service logic

## ➡️ Controller class and Corresponding Controller Test Class

### 🟦 controller class

```java
@RestController
@RequestMapping("/users")
public class UserController {

    private final UserService userService;

    public UserController(UserService userService) {
        this.userService = userService;
    }

    @GetMapping("/{id}")
    public User getUserById(@PathVariable Long id) {
        return userService.getUserById(id);
    }
}

```

### 🟦 controller test

```java
@WebMvcTest(UserController.class)
class UserControllerTest {

    @Autowired
    private MockMvc mockMvc;

    @MockBean
    private UserService userService;

    @Test
    void shouldReturnUserById() throws Exception {

        User user = new User(1L, "Gulam");

        Mockito.when(userService.getUserById(1L))
               .thenReturn(user);

        mockMvc.perform(get("/users/1"))
               .andExpect(status().isOk())
               .andExpect(jsonPath("$.name").value("Gulam"));
    }
}

```

- **If spring return type is String**

```java
@GetMapping("/hello")
public String helloUser() {
    return "Hello Gulam";
}
```

```java
    mockMvc.perform(get("/users/hello"))
           .andExpect(status().isOk())
           .andExpect(content().string("Hello Gulam"));

```
