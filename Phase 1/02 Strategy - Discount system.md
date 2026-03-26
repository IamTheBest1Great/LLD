Below is a well‑organized file and folder structure for the discount system using the Strategy Pattern, along with the complete code for each file and an explanation of how they work together.

## Folder Structure

```
discount-system/
├── pom.xml                     (optional, for Maven)
└── src/
    └── main/
        └── java/
            └── com/
                └── example/
                    └── discount/
                        ├── DiscountStrategy.java
                        ├── FlatDiscount.java
                        ├── PercentageDiscount.java
                        ├── Order.java
                        └── Main.java
```

If you are not using Maven, you can simply create the Java files in the corresponding directories and compile/run them manually.

---

## File Contents

### 1. `DiscountStrategy.java` – The Strategy Interface

```java
package com.example.discount;

/**
 * The strategy interface declares the operation common to all
 * supported discount algorithms.
 */
public interface DiscountStrategy {
    double applyDiscount(double originalPrice);
}
```

**Explanation:**  
This interface defines the contract for all discount strategies. Any concrete discount must implement `applyDiscount()`.

---

### 2. `FlatDiscount.java` – Concrete Strategy (Flat Discount)

```java
package com.example.discount;

/**
 * Flat discount: subtracts a fixed amount from the original price.
 */
public class FlatDiscount implements DiscountStrategy {
    private final double discountAmount;

    public FlatDiscount(double discountAmount) {
        this.discountAmount = discountAmount;
    }

    @Override
    public double applyDiscount(double originalPrice) {
        // Ensure price does not become negative
        return Math.max(0, originalPrice - discountAmount);
    }
}
```

**Explanation:**  
This class implements a fixed‑amount discount. The discount amount is passed via the constructor and applied in `applyDiscount()`.

---

### 3. `PercentageDiscount.java` – Concrete Strategy (Percentage Discount)

```java
package com.example.discount;

/**
 * Percentage discount: subtracts a percentage of the original price.
 */
public class PercentageDiscount implements DiscountStrategy {
    private final double percentage; // e.g., 10 for 10%

    public PercentageDiscount(double percentage) {
        this.percentage = percentage;
    }

    @Override
    public double applyDiscount(double originalPrice) {
        double discount = originalPrice * (percentage / 100);
        return originalPrice - discount;
    }
}
```

**Explanation:**  
This class applies a percentage discount. The percentage is provided at construction, and the discount is calculated as a proportion of the original price.

---

### 4. `Order.java` – The Context

```java
package com.example.discount;

/**
 * The context maintains a reference to a discount strategy and
 * delegates the discount calculation to it.
 */
public class Order {
    private final double totalAmount;
    private DiscountStrategy discountStrategy;

    public Order(double totalAmount) {
        this.totalAmount = totalAmount;
    }

    public void setDiscountStrategy(DiscountStrategy strategy) {
        this.discountStrategy = strategy;
    }

    public double calculateFinalAmount() {
        if (discountStrategy == null) {
            return totalAmount; // no discount applied
        }
        return discountStrategy.applyDiscount(totalAmount);
    }
}
```

**Explanation:**  
The `Order` class is the context. It holds a reference to a `DiscountStrategy` (which can be set at runtime). When `calculateFinalAmount()` is called, it delegates the discount logic to the current strategy. This keeps the order class independent of specific discount calculations.

---

### 5. `Main.java` – Client Code

```java
package com.example.discount;

public class Main {
    public static void main(String[] args) {
        // Create an order with a total of 1000
        Order order = new Order(1000);

        // Apply a flat discount of $200
        order.setDiscountStrategy(new FlatDiscount(200));
        System.out.println("After flat discount: $" + order.calculateFinalAmount()); // 800.0

        // Switch to a 15% percentage discount
        order.setDiscountStrategy(new PercentageDiscount(15));
        System.out.println("After 15% discount: $" + order.calculateFinalAmount()); // 850.0
    }
}
```

**Explanation:**  
The client creates an `Order` and selects the discount strategy by calling `setDiscountStrategy()`. It then calculates the final amount. No `if-else` statements are needed; the strategy is injected polymorphically.

---

## How the Pattern Works Together

1. **Strategy Interface (`DiscountStrategy`)** – defines a common method for all discount algorithms.
2. **Concrete Strategies (`FlatDiscount`, `PercentageDiscount`)** – each implements the discount logic in its own way.
3. **Context (`Order`)** – holds a reference to a `DiscountStrategy` and delegates the calculation to it. The context never knows which concrete strategy it is using – it just calls the interface method.
4. **Client (`Main`)** – decides which strategy to use and sets it on the context. This decision can be made at runtime based on user input, configuration, or any other condition, without modifying the `Order` class.

---

## Benefits of This Structure

- **Open/Closed Principle** – To add a new discount (e.g., “Buy One Get One”), you simply create a new class implementing `DiscountStrategy`. No existing code needs to change.
- **Single Responsibility** – Each strategy class encapsulates one specific discount algorithm, and the `Order` class focuses only on order management.
- **Testability** – Each strategy can be unit‑tested in isolation.
- **Runtime Flexibility** – Strategies can be swapped on the fly (as shown in `Main`).

This structure makes the discount system highly maintainable and extensible, a perfect illustration of the Strategy Pattern in a real‑world scenario.
