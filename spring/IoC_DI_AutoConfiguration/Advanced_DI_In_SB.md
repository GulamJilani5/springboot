# Handling Multiple Implementations of an Interface

- If we multiple implementations of **PaymentService** (e.g., `CreditCardPaymentService` and `PayPalPaymentService`), We can use **@Qualifier** to specify which one to inject:

```java
@Service
public class OrderService {
    private final PaymentService paymentService;

    public OrderService(@Qualifier("creditCardPaymentService") PaymentService paymentService) {
        this.paymentService = paymentService;
    }
}
```

**Alternatively, mark one implementation as @Primary:**

```java
@Service
@Primary
public class CreditCardPaymentService implements PaymentService {
    // ...
}
```

---

### Lazy Initialization

- By default, Spring creates all singleton beans at startup. To defer creation until a bean is needed, use @Lazy:

```java
@Service
@Lazy
public class HeavyService {
    // Initialized only when first accessed
}
```
