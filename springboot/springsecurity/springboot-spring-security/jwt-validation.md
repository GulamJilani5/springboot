⏺️ ➡️ 🟦 🟩 🟢 🔵 🔷 🔹🔴 ☑️ ✔️ ✓→•←⁕⁂※⁜‣

# ⏺️ JWT Validation

```text
GET /api/secure-data
Authorization: Bearer <token>

1. SecurityConfig -- SecurityFilterChain
   -> checks authentication required or not
   .anyRequest().authenticated() - token required
2. JwtAuthFilter
   shouldNotFilter()
   -> path != singup/login, it returns false, so jwt filter required.
   doFilterInternal()
   -> extract token from header
   -> extract username from token
   -> check SecurityContext (if not authenticated)
   -> UserDetailsService.loadUserByUsername(username)
   -> jwtUtils.validateToken(token, userDetails)
   -> if valid:
   create UsernamePasswordAuthenticationToken
   SecurityContextHolder.setAuthentication()
   -> filterChain.doFilter(request, response)
3. Controller
   -> request is already authenticated
```

### Explanation

- What this Does

```java
   if valid:
    - create UsernamePasswordAuthenticationToken
    - SecurityContextHolder.setAuthentication()
```

```java
UsernamePasswordAuthenticationToken authToken = new UsernamePasswordAuthenticationToken(userDetails, null,userDetails.getAuthorities()
);
SecurityContextHolder.getContext().setAuthentication(authToken);
```

- Create an Authentication object (represents logged-in user)
- Put it into Spring’s SecurityContext
- If Token is valid
  - 1.  Create object:
        - "This is USER john"
  - 2.  Tell Spring:
        - "john is authenticated for this request"
  - 3.  Now request is allowed
