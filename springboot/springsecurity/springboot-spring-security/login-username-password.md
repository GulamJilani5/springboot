⏺️ ➡️ 🟦 🟩 🟢 🔵 🔷 🔹🔴 ☑️ ✔️ ✓→•←⁕⁂※⁜‣

# ⏺️ Login - Username/Password

```text
POST /user/login

1. SecurityFilterChain
   -> checks endpoint permission (permitAll for login)

2. JwtAuthFilter
   -> skipped (shouldNotFilter = true)

3. Controller
    -> calls AuthenticationManager.authenticate()

    4. AuthenticationManager
       -> delegates to AuthenticationProvider (DaoAuthenticationProvider)

    5. DaoAuthenticationProvider (internally used by Spring)
       -> UserDetailsService.loadUserByUsername(username)
       -> PasswordEncoder.matches(password, DB password)

    6. If valid:
      -> returns authenticated Authentication object

7. Controller
    -> get UserDetails from Authentication
    -> jwtUtils.generateToken(userDetails)

8. Response
    -> send JWT token to client
```
