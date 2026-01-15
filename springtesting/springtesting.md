⏺️ ➡️ 🟦 🔵 🟢🔴⭕🟠🟣🟥🟧✔️ ☑️ • ‣ → ⁕

# ⏺️ Spring Boot Test

- `spring-boot-starter-test` to include testing frameworks like **JUnit**, **Mockito**, and Spring Test for unit and integration testing.

### 🟦 Overall Authentication Testing Flow

```text
Controller Test
   ↓
Validates API behavior

Service Test
   ↓
Validates business logic

Integration Test
   ↓
Validates complete system

```

## ➡️ Controller Testing — API Contract Validation

- Controller tests ensure the REST API behaves correctly and returns proper HTTP responses.
- What it tests

  - HTTP request & response behavior
  - Request validation (`@Valid`)
  - Correct HTTP status codes
  - JSON structure
  - Exception mapping

- What it does NOT test
  - Business logic
  - Database
  - Security internals
- How it works

  - Uses `@WebMvcTest`
  - Uses MockMvc
  - Mocks the service layer

- **Flow**

```
 HTTP Request
   ↓
Controller
   ↓
(Mocked) Service
   ↓
HTTP Response

```

- **Example Explanation (Signup)**

  - A POST request is sent to `/auth/signup`
  - Controller receives JSON
  - Validates fields (email, password)
  - Calls `authService.signup()`
  - Service is mocked → no DB hit
  - Controller returns **201** CREATED

#### 🟦 Signup Controller Test

- **Flow**

```text
 Mock HTTP Request
   ↓
AuthController
   ↓
Mock AuthService
   ↓
HTTP Response
```

- **Code**

```java
 @WebMvcTest(AuthController.class)
class AuthControllerTest {

    @Autowired
    private MockMvc mockMvc;

    @MockBean
    private AuthService authService;

    @Autowired
    private ObjectMapper objectMapper;

    @Test
    void signup_success() throws Exception {
        SignupRequest request =
                new SignupRequest("test@gmail.com", "Password@123");

        Mockito.doNothing().when(authService).signup(any());

        mockMvc.perform(post("/auth/signup")
                .contentType(MediaType.APPLICATION_JSON)
                .content(objectMapper.writeValueAsString(request)))
                .andExpect(status().isCreated());
    }

    @Test
    void signup_emailAlreadyExists() throws Exception {
        SignupRequest request =
                new SignupRequest("test@gmail.com", "Password@123");

        Mockito.doThrow(new EmailAlreadyExistsException("Exists"))
                .when(authService).signup(any());

        mockMvc.perform(post("/auth/signup")
                .contentType(MediaType.APPLICATION_JSON)
                .content(objectMapper.writeValueAsString(request)))
                .andExpect(status().isConflict());
    }
}

```

## ➡️ Service Testing — Business Logic Validation

- Service tests ensure authentication logic works correctly without involving HTTP or database.
- **What it tests**

  - Core authentication logic
  - Password encoding
  - Credential validation
  - Exception throwing
  - Token generation logic (mocked)

- **What it does NOT test**

  - HTTP
  - JSON
  - Request/response mapping

- **How it works**

  - Uses **@ExtendWith(MockitoExtension.class)**
  - Repository and encoder are mocked
  - No Spring context required

- **Flow**

```text
Service Method
   ↓
(Mocked) Repository
   ↓
(Mocked) PasswordEncoder
   ↓
Return / Exception

```

- **Example Explanation (Login)**

  - Service receives email & password
  - Fetches user from repository
  - Matches password using encoder
  - Generates JWT token
  - Returns token or throws BadCredentialsException

- **Service Test Flow**

```text
 Service Method
   ↓
Mock Repository
   ↓
Mock PasswordEncoder
   ↓
Return / Exception

```

#### 🟦 Signup Service Test

```java
@ExtendWith(MockitoExtension.class)
class AuthServiceTest {

    @Mock
    private UserRepository userRepository;

    @Mock
    private PasswordEncoder passwordEncoder;

    @InjectMocks
    private AuthService authService;

    @Test
    void signup_shouldSaveUserWithEncodedPassword() {

        SignupRequest request =
                new SignupRequest("test@gmail.com", "Password@123");

        Mockito.when(userRepository.existsByEmail(request.getEmail()))
                .thenReturn(false);

        Mockito.when(passwordEncoder.encode(any()))
                .thenReturn("encodedPassword");

        authService.signup(request);

        Mockito.verify(userRepository).save(
                argThat(user ->
                        user.getPassword().equals("encodedPassword")
                )
        );
    }
}

```

#### 🟦 Login Service Test

```java
@Test
void login_wrongPassword_shouldThrowException() {

    User user = new User();
    user.setEmail("test@gmail.com");
    user.setPassword("encoded");

    Mockito.when(userRepository.findByEmail(any()))
            .thenReturn(Optional.of(user));

    Mockito.when(passwordEncoder.matches(any(), any()))
            .thenReturn(false);

    assertThrows(BadCredentialsException.class,
            () -> authService.login(
                    new LoginRequest("test@gmail.com", "wrong")));
}

```

## ➡️ Integration Testing — End-to-End Flow Validation

- Integration tests validate the complete authentication flow including security and database
- **What it tests**

  - Full application context
  - Real DB (H2/Testcontainers)
  - Spring Security filters
  - JWT generation & validation
  - Exception handlers

- **How it works**

  - Uses `@SpringBootTest`
  - Uses `@AutoConfigureMockMvc`
  - Minimal mocking

- **Flow**

```text
HTTP Request
   ↓
Security Filter Chain
   ↓
Controller
   ↓
Service
   ↓
Repository
   ↓
Database
   ↓
JWT Generation
   ↓
HTTP Response

```

- **Example Explanation**

  - Signup API creates user in DB
  - Login API authenticates user
  - JWT token is returned
  - Token can be used for secured endpoints

- **Code**

```java
@SpringBootTest
@AutoConfigureMockMvc
class AuthIntegrationTest {

    @Autowired
    private MockMvc mockMvc;

    @Test
    void fullSignupAndLoginFlow() throws Exception {

        // Signup
        mockMvc.perform(post("/auth/signup")
                .contentType(MediaType.APPLICATION_JSON)
                .content("""
                    {
                      "email": "int@test.com",
                      "password": "Password@123"
                    }
                """))
                .andExpect(status().isCreated());

        // Login
        mockMvc.perform(post("/auth/login")
                .contentType(MediaType.APPLICATION_JSON)
                .content("""
                    {
                      "email": "int@test.com",
                      "password": "Password@123"
                    }
                """))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.token").exists());
    }
}

```
