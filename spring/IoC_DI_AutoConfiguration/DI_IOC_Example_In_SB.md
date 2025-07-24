# Example 1: Basic DI with Constructor Injection

Suppose we have a **OrderService** that depends on a **PaymentService** to process payments.

```java

// PaymentService interface
public interface PaymentService {
    String processPayment(double amount);
}

// Concrete implementation of PaymentService
@Service
public class CreditCardPaymentService implements PaymentService {
    @Override
    public String processPayment(double amount) {
        return "Processed payment of $" + amount + " via Credit Card";
    }
}

// OrderService that depends on PaymentService
@Service
public class OrderService {
    private final PaymentService paymentService;

    // Constructor injection
    public OrderService(PaymentService paymentService) {
        this.paymentService = paymentService;
    }

    public String placeOrder(double amount) {
        return paymentService.processPayment(amount);
    }
}
```

### What’s Happening Here?

- The `@Service` annotation marks **CreditCardPaymentService** and **OrderService** as Spring-managed beans.
- The Spring IoC container detects that **OrderService** has a constructor that requires a **PaymentService** dependency.
- At runtime, the IoC container creates an instance of **CreditCardPaymentService** (since it implements **PaymentService**) and injects it into **OrderService** via the constructor.
- The **OrderService** doesn’t need to create the **PaymentService** itself — this is **IOC contianer** in action, as control is inverted to the **Spring container**.

### How to Test This?

- We can create a REST controller to expose the **OrderService:**

```java
@RestController
@RequestMapping("/orders")
public class OrderController {
    private final OrderService orderService;

    // Constructor injection
    public OrderController(OrderService orderService) {
        this.orderService = orderService;
    }

    @PostMapping("/place")
    public String placeOrder(@RequestParam double amount) {
        return orderService.placeOrder(amount);
    }
}
```

When we run the Spring Boot application and send a POST request to /orders/place?amount=100, the IoC container ensures that:

- An **OrderService** bean is created with a **PaymentService** dependency injected.
- The **OrderController** is created with the **OrderService** injected.
- The request is processed, and the response might be: "Processed payment of $100 via Credit Card".

---

# Example 2: Using @Autowired for Field Injection

While constructor injection is preferred, we can also use **field injection** for simplicity (though it’s less recommended due to testability and immutability concerns):

```java
@Service
public class OrderService {
    @Autowired
    private PaymentService paymentService;

    public String placeOrder(double amount) {
        return paymentService.processPayment(amount);
    }
}
```

Here, the @Autowired annotation tells the Spring IoC container to inject a PaymentService bean directly into the paymentService field. The container resolves the dependency at runtime.
