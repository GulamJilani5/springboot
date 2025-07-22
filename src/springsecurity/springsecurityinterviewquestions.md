# Spring Security
### Below are the top 10 one-liners Spring Security questions which are often asked in Spring Boot Interviews:
##### Q1: What's the difference between authentication and authorization?
Authentication verifies WHO you are (username/password), authorization determines WHAT you can do (roles/permissions). Authentication comes first, authorization follows.
##### Q2: Why use JWT tokens instead of sessions?
JWT is stateless (no server-side storage), scalable across multiple servers, and contains user info.
Sessions require server-side storage but are easier to revoke immediately.
##### Q3: What's @PreAuthorize vs @Secured vs @RolesAllowed?
**@PreAuthorize** supports SpEL expressions (most flexible), **@Secured** is Spring-specific for simple role checks, **@RolesAllowed** is JSR-250 standard. **@PreAuthorize** is preferred.
##### Q4: How does Spring Security handle CSRF protection?
Automatically enabled for state-changing operations (**POST, PUT, DELETE**). Generates **CSRF** tokens that must be included in requests. Disable for stateless APIs using JWT.
##### Q5: What's the security filter chain order?
Security filters run in specific order: `CORS → CSRF`
**→ Authentication → Authorization**. Custom filters are added relative to existing ones using addFilterBefore/After.
##### Q6: How do you secure method parameters and return values?
**@PreAuthorize** for parameters **(#param)**, **@PostAuthorize** for return values (returnObject), **@PostFilter** for collections, **@PreFilter** for input collections.
##### Q7: Why BCryptPasswordEncoder over plain text passwords?
**BCrypt** uses adaptive hashing with salt, making it computationally expensive to crack. Each hash is unique even for identical passwords, preventing rainbow table attacks.
##### Q8: How do you implement custom authentication providers?
Extend Authentication Provider interface, override **authenticate()** and supports() methods. Register via WebSecurity Configurer or **@Bean** annotation.
##### Q9: OAuth2 vs Basic Authentication - when to use what?
Basic auth for simple internal APIs (base64 encoded credentials). **OAuth2** for third-party integrations, mobile apps, or when you need granular scopes and token refresh capabilities.
##### Q10: How does remember-me functionality work?
Stores persistent token (usually in database) tied to user session. **Auto-authenticates** on subsequent visits via `cookie`. Configure with **rememberMe()** in security config.
