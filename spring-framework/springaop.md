⏺️ ➡️ 🟦 🔵 🟢🔴⭕🟠🟣🟥🟧✔️ ☑️ • ‣ → ⁕

# ⏺️ Annotations based on Spring AOP proxies:

@Async, @Transactional, @Cacheable, @PreAuthorize, @Retryable, @CircuitBreaker (Spring Retry/Resilience4J)
@Scheduled (slightly different)

- These work ONLY when the method is invoked from OUTSIDE the class, i.e. from another bean through the Spring proxy.
