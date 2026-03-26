Below is a complete design for a **shopping cart pricing system** using the Strategy Pattern. This is a common interview question that tests your ability to apply design patterns to model flexible business rules.

## Overview

The pricing system should allow different pricing strategies to be applied to items in a shopping cart. For example:

- **Regular Price** – no discount.
- **Seasonal Sale** – a percentage off all items.
- **Member Discount** – a fixed percentage for members.
- **Buy One Get One (BOGO)** – a special promotion on certain items.

The Strategy Pattern encapsulates each pricing algorithm into its own class, making the system open for extension (new pricing strategies) but closed for modification (existing code remains unchanged).

## Folder Structure

```
shopping-cart-pricing/
└── src/
    └── main/
        └── java/
            └── com/
                └── example/
                    └── pricing/
                        ├── PricingStrategy.java
                        ├── RegularPricing.java
                        ├── SalePricing.java
                        ├── MemberDiscountPricing.java
                        ├── BogoPricing.java
                        ├── CartItem.java
                        ├── ShoppingCart.java
                        └── Main.java
```

---

## File Contents

### 1. `PricingStrategy.java` – Strategy Interface

```java
package com.example.pricing;

import java.util.List;

/**
 * Strategy interface for calculating the total price of a list of cart items.
 */
public interface PricingStrategy {
    double calculateTotal(List<CartItem> items);
}
```

---

### 2. `CartItem.java` – Data Class

```java
package com.example.pricing;

public class CartItem {
    private String name;
    private double price;
    private int quantity;

    public CartItem(String name, double price, int quantity) {
        this.name = name;
        this.price = price;
        this.quantity = quantity;
    }

    public String getName() { return name; }
    public double getPrice() { return price; }
    public int getQuantity() { return quantity; }

    public double getSubtotal() {
        return price * quantity;
    }
}
```

---

### 3. Concrete Pricing Strategies

#### `RegularPricing.java` – No discount

```java
package com.example.pricing;

import java.util.List;

public class RegularPricing implements PricingStrategy {
    @Override
    public double calculateTotal(List<CartItem> items) {
        return items.stream().mapToDouble(CartItem::getSubtotal).sum();
    }
}
```

#### `SalePricing.java` – Percentage discount on all items

```java
package com.example.pricing;

import java.util.List;

public class SalePricing implements PricingStrategy {
    private double discountPercentage; // e.g., 10 for 10%

    public SalePricing(double discountPercentage) {
        this.discountPercentage = discountPercentage;
    }

    @Override
    public double calculateTotal(List<CartItem> items) {
        double subtotal = items.stream().mapToDouble(CartItem::getSubtotal).sum();
        return subtotal * (1 - discountPercentage / 100);
    }
}
```

#### `MemberDiscountPricing.java` – Fixed percentage for members

```java
package com.example.pricing;

import java.util.List;

public class MemberDiscountPricing implements PricingStrategy {
    private double memberDiscountPercentage; // e.g., 5 for 5%

    public MemberDiscountPricing(double memberDiscountPercentage) {
        this.memberDiscountPercentage = memberDiscountPercentage;
    }

    @Override
    public double calculateTotal(List<CartItem> items) {
        double subtotal = items.stream().mapToDouble(CartItem::getSubtotal).sum();
        return subtotal * (1 - memberDiscountPercentage / 100);
    }
}
```

#### `BogoPricing.java` – Buy One Get One free (on specific items)

```java
package com.example.pricing;

import java.util.List;

public class BogoPricing implements PricingStrategy {
    private String eligibleItemName; // e.g., "T-Shirt"

    public BogoPricing(String eligibleItemName) {
        this.eligibleItemName = eligibleItemName;
    }

    @Override
    public double calculateTotal(List<CartItem> items) {
        double total = 0;
        for (CartItem item : items) {
            if (item.getName().equalsIgnoreCase(eligibleItemName)) {
                // BOGO: for every 2 items, pay for 1
                int paidQuantity = (item.getQuantity() / 2) + (item.getQuantity() % 2);
                total += paidQuantity * item.getPrice();
            } else {
                total += item.getSubtotal();
            }
        }
        return total;
    }
}
```

---

### 4. `ShoppingCart.java` – Context

```java
package com.example.pricing;

import java.util.ArrayList;
import java.util.List;

/**
 * The context holds a list of items and a pricing strategy.
 * It delegates the total calculation to the strategy.
 */
public class ShoppingCart {
    private List<CartItem> items;
    private PricingStrategy pricingStrategy;

    public ShoppingCart() {
        this.items = new ArrayList<>();
    }

    public void addItem(CartItem item) {
        items.add(item);
    }

    public void setPricingStrategy(PricingStrategy strategy) {
        this.pricingStrategy = strategy;
    }

    public double getTotal() {
        if (pricingStrategy == null) {
            // Default to regular pricing if no strategy set
            return new RegularPricing().calculateTotal(items);
        }
        return pricingStrategy.calculateTotal(items);
    }
}
```

---

### 5. `Main.java` – Client Code

```java
package com.example.pricing;

public class Main {
    public static void main(String[] args) {
        ShoppingCart cart = new ShoppingCart();
        cart.addItem(new CartItem("T-Shirt", 20.00, 3));
        cart.addItem(new CartItem("Jeans", 50.00, 1));
        cart.addItem(new CartItem("Socks", 5.00, 2));

        // 1. Regular pricing
        cart.setPricingStrategy(new RegularPricing());
        System.out.println("Regular total: $" + cart.getTotal()); // 20*3 + 50 + 5*2 = 120

        // 2. 20% sale
        cart.setPricingStrategy(new SalePricing(20));
        System.out.println("Sale total (20% off): $" + cart.getTotal()); // 120 * 0.8 = 96

        // 3. Member discount 10%
        cart.setPricingStrategy(new MemberDiscountPricing(10));
        System.out.println("Member total (10% off): $" + cart.getTotal()); // 120 * 0.9 = 108

        // 4. BOGO on T-Shirt
        cart.setPricingStrategy(new BogoPricing("T-Shirt"));
        System.out.println("BOGO total (T-Shirt BOGO): $" + cart.getTotal());
        // T-Shirt: 3 items -> pay for 2 = 40, plus Jeans 50, plus Socks 10 = 100
    }
}
```

**Output:**

```
Regular total: $120.0
Sale total (20% off): $96.0
Member total (10% off): $108.0
BOGO total (T-Shirt BOGO): $100.0
```

---

## How the Pattern Works

- **Strategy Interface (`PricingStrategy`)** – defines a method `calculateTotal()` that takes a list of items and returns the total price.
- **Concrete Strategies (`RegularPricing`, `SalePricing`, etc.)** – implement the algorithm for each pricing rule.
- **Context (`ShoppingCart`)** – holds a reference to a `PricingStrategy` and delegates the total calculation to it. The cart doesn’t know which strategy it is using.
- **Client (`Main`)** – decides which strategy to apply based on business conditions (e.g., user membership, current promotion, etc.) and sets it on the cart.

## Benefits & Interview Points

- **Open/Closed Principle** – adding a new pricing rule (e.g., "Loyalty points discount") only requires creating a new class; no changes to `ShoppingCart` or existing strategies.
- **Replaces conditional logic** – instead of having a `calculateTotal()` method with if‑else for each pricing type, we inject the appropriate strategy.
- **Testability** – each pricing strategy can be unit‑tested independently.
- **Runtime flexibility** – strategies can be changed on the fly, e.g., when a user applies a coupon code.
- **Single Responsibility** – each class has one reason to change (the pricing algorithm).

This design is a clean, maintainable solution for a shopping cart pricing system and is frequently discussed in software design interviews.
