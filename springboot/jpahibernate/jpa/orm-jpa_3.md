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

## ➡️ Does @Transactional(readOnly = true) prevent dirty checking?

Yes — it effectively prevents dirty checking because Hibernate does NOT perform dirty checking for read-only transactions.

### 🟦 Extra Performance Hibernate Doesn’t Need to Spend

| Performance Cost                    | Required in readOnly? |
| ----------------------------------- | --------------------- |
| Create snapshots                    | ❌ No                 |
| Dirty checking                      | ❌ No                 |
| Flushing                            | ❌ No                 |
| Generating update SQL               | ❌ No                 |
| Tracking collection changes         | ❌ No                 |
| Managing versioning                 | ❌ No                 |
| Extra memory in Persistence Context | ❌ Reduced            |

## ➡️ Difference Between REQUIRED and REQUIRES_NEW

### 🟦 @Transactional(propagation = REQUIRED)

- Default behavior in Spring.
- If a transaction already exists:

  - The method joins the existing transaction.

- If no transaction exists:

  - A new transaction is started.

- Rollback behavior:

  - If the outer transaction rolls back, everything inside (including this method) also rolls back.

- Use case:
  - When you want all operations to be part of a single logical unit of work.

```java
   @Service
   public class OrderService {

    @Transactional  // REQUIRED by default
    public void placeOrder() {
        saveOrder(); // participates in same TX

        paymentService.debitAccount(); // if this fails → whole TX rolls back
    }
}

```

### 🟦 @Transactional(propagation = REQUIRES_NEW)

- Always starts a new, independent transaction.

- If a transaction already exists:

  - The existing transaction is suspended.
  - A new transaction is created for this method.

- When this method completes:

  - The new transaction is committed or rolled back independently of the outer transaction.
  - The outer transaction is then resumed.

- Rollback behavior:

  - Rollback in the outer transaction does not affect this inner transaction.

- Use case:

  - When you want a method to commit changes regardless of the outer transaction’s outcome.
  - **Example:** Logging, audit trails, sending notifications — things that should persist even if the main business transaction fails.

  ```java
  @Service
  public class PaymentService {

    @Transactional(propagation = Propagation.REQUIRES_NEW)
    public void debitAccount() {
        // Runs in its OWN transaction
    }
  }

  ```

  ```java
      @Service
  public class UserService {

    @Transactional(propagation = Propagation.REQUIRED)
    public void processUser(User user) {
        // Outer transaction starts here
        saveUser(user);  // Joins outer transaction
        logActivity(user);  // Joins outer transaction
    }

    @Transactional(propagation = Propagation.REQUIRED)  // Default is REQUIRED
    public void saveUser(User user) {
        // Joins the outer transaction from processUser
        userRepository.save(user);
    }

    @Transactional(propagation = Propagation.REQUIRES_NEW)
    public void logActivity(User user) {
        // New transaction created; outer is suspended
        activityRepository.save(new Activity(user.getId(), "Processed"));
        // Commits independently, even if processUser later fails
    }
  }
  ```
