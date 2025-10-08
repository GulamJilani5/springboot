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

When we run the Spring Boot application and send a POST request to `/orders/place?amount=100`, the **IoC container** ensures that:

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

Here, the `@Autowired` annotation tells the **Spring IoC container** to inject a **PaymentService** bean directly into the **paymentService** field. The container resolves the dependency at runtime.

---

# Example 3: Component Scanning and Bean Configuration

- When we annotate a class with @Component, @Service, @Repository, or @Controller, Spring automatically detects it during component scanning and registers it as a bean in the IoC container.

For example **repository** for database access:

```java
@Repository
public class OrderRepository {
    public void saveOrder(String orderDetails) {
        // Simulate saving to a database
        System.out.println("Saving order: " + orderDetails);
    }
}
```

And a service that depends on it:

```java
@Service
public class OrderService {
    private final OrderRepository orderRepository;
    private final PaymentService paymentService;

    public OrderService(OrderRepository orderRepository, PaymentService paymentService) {
        this.orderRepository = orderRepository;
        this.paymentService = paymentService;
    }

    public String placeOrder(double amount, String orderDetails) {
        orderRepository.saveOrder(orderDetails);
        return paymentService.processPayment(amount);
    }
}

```

When the Spring Boot application starts:

- The IoC container scans for `@Repository`, `@Service`, and other annotations.
- It creates beans for **OrderRepository**, **PaymentService**, and **OrderService**.
- It injects **OrderRepository** and **PaymentService** into **OrderService** via constructor injection.

---

# Example 4: Configuring Beans Explicitly

Sometimes, we need to define beans explicitly (e.g., for third-party libraries or custom configurations). We can use a `@Configuration` class:

```java
@Configuration
public class AppConfig {

    @Bean
    public PaymentService paymentService() {
        return new CreditCardPaymentService();
    }

    @Bean
    public OrderService orderService(PaymentService paymentService) {
        return new OrderService(paymentService);
    }
}
```

Here:

- The `@Configuration` class tells Spring that this class contains bean definitions.
- The `@Bean` annotation defines a bean explicitly.
- The **IoC container** creates and manages these beans, injecting **PaymentService** into **OrderService**.
  This approach is useful when you need fine-grained control over bean creation or when dealing with classes you can’t annotate (**e.g.**, `third-party libraries`).
