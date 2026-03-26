Below is a complete, runnable example of a discount system using the Strategy Pattern. The system supports two discount strategies: **Flat Discount** (a fixed amount off) and **Percentage Discount** (a percentage off). The pattern allows adding new discount types later without modifying existing code.

## Code Example (Java)

### Step 1: Define the Strategy Interface

```java
// The common interface for all discount strategies
interface DiscountStrategy {
    double applyDiscount(double originalPrice);
}
```

### Step 2: Implement Concrete Strategies

```java
// Flat discount: subtracts a fixed amount
class FlatDiscount implements DiscountStrategy {
    private double discountAmount;

    public FlatDiscount(double discountAmount) {
        this.discountAmount = discountAmount;
    }

    @Override
    public double applyDiscount(double originalPrice) {
        // Ensure price doesn't go negative
        return Math.max(0, originalPrice - discountAmount);
    }
}

// Percentage discount: subtracts a percentage of the original price
class PercentageDiscount implements DiscountStrategy {
    private double percentage; // e.g., 10 for 10%

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

### Step 3: Create the Context

The context (here an `Order` class) holds a reference to a discount strategy and delegates the discount calculation to it. The context is unaware of which specific strategy it uses.

```java
class Order {
    private double totalAmount;
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

### Step 4: Use the Pattern

```java
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

**Output:**

```
After flat discount: $800.0
After 15% discount: $850.0
```

## Explanation

- **DiscountStrategy** – the interface that all discount algorithms must implement. It declares `applyDiscount()`.
- **FlatDiscount / PercentageDiscount** – concrete implementations, each providing a specific discount calculation.
- **Order** – the context. It uses a `DiscountStrategy` to compute the final amount. The strategy can be changed at runtime (e.g., based on user type, promotion, etc.).
- **No conditional statements** inside `calculateFinalAmount()` – polymorphism does the work.

## How It Replaces If-Else

Without the Strategy Pattern, you might have written:

```java
public double calculateFinalAmount(String discountType, double discountValue) {
    if ("flat".equals(discountType)) {
        return Math.max(0, totalAmount - discountValue);
    } else if ("percentage".equals(discountType)) {
        return totalAmount - (totalAmount * discountValue / 100);
    }
    return totalAmount;
}
```

Every time you added a new discount type, you would have to modify this method. With the Strategy Pattern, you simply add a new class implementing `DiscountStrategy` and pass it to the order – no existing code changes.

## Benefits

- **Open/Closed Principle** – the system is open for extension (new discounts) but closed for modification (existing classes remain unchanged).
- **Single Responsibility** – each strategy encapsulates its own discount logic; the order class only manages the total and delegates.
- **Runtime Flexibility** – strategies can be swapped on the fly.
- **Testability** – each strategy can be unit‑tested independently.

This is a classic and practical example of the Strategy Pattern in action.
