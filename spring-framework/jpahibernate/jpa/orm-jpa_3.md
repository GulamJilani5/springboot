🔵🟢🔴➡️⭕🟠🟦🟣🟥🟧✔️⏺️ ☑️ • ‣ → ⁕

# ⏺️ @Transactional

- Find more about `@Transactional` in the `transactional.jpeg`.
  Spring automatically wraps the method in a **proxy object** (using **JDK Dynamic Proxy or CGLIB**) that handles the transaction lifecycle.
- This process include:
  - **Opening a transaction** before the method executes.
  - **Committing the transaction** if the execution is successful.
  - **Rolling back the transaction** automatically if an exception occurs.

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

@Service
public class OrderService {

    @Autowired
    private OrderRepository orderRepository;

    @Transactional
    public void createOrder(String orderId, double amount) {
        Order order = new Order(orderId, amount);
        orderRepository.save(order); // Simulates saving to database
        // If successful, transaction commits automatically
        if (amount < 0) {
            throw new IllegalArgumentException("Negative amount not allowed");
        }
    }
}
```

- The `createOrder` method is annotated with **@Transactional**.
- When called, Spring creates a proxy that opens a transaction before `orderRepository.save(order)` executes.
- If an exception (e.g., negative amount) is thrown, the transaction rolls back automatically. Otherwise, it commits after successful execution.

## ➡️ Advantage

A key advantage is that Spring handles all the boilerplate code for connection handling, commit, and rollback through the **TransactionInterceptor** and **PlatformTransactionManager**, making database operations safe and atomic without manual intervention.

## ➡️ Common Pitfall

However, a common pitfall to mention is that if a @Transactional method is called from another method within the same class, the proxy mechanism doesn’t intercept it, resulting in no transaction. To avoid this, you should move the method to a different bean or call it through the Spring proxy.

```java
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

@Service
public class OrderService {

    @Transactional
    public void createOrder(String orderId, double amount) {
        saveOrder(orderId, amount); // Internal method call
    }

    public void saveOrder(String orderId, double amount) {
        Order order = new Order(orderId, amount);
        // Simulates database save
        // No transaction here because proxy doesn't intercept
    }
}
```

- Here, createOrder calls saveOrder internally within the same class.
- Since the call is not intercepted by the Spring proxy (which only works for external calls to the bean), no transaction is started, and saveOrder executes without transactional support. This can lead to inconsistent data if an exception occurs

### 🟦 Fix for the Pitfall

- To resolve this, move saveOrder to a different service or call it through the proxy:

```java
  @Service
public class OrderService {

    @Autowired
    private OrderService self; // Inject self for proxy call

    @Transactional
    public void createOrder(String orderId, double amount) {
        self.saveOrder(orderId, amount); // Proxy intercepts this
    }

    public void saveOrder(String orderId, double amount) {
        Order order = new Order(orderId, amount);
        // Simulates database save
    }
}
```

- Using **self** ensures the call goes through the proxy, enabling transactional behavior.
