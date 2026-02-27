## Day 4 — Abstract Classes vs Interfaces

Today we'll clarify two fundamental constructs in Java: **abstract classes** and **interfaces**. Both are used to achieve abstraction and polymorphism, but they serve different purposes. Understanding when to use each is crucial for designing clean, maintainable code.

---

### 1. Abstract Classes

An **abstract class** is a class that cannot be instantiated. It can contain:

- **Abstract methods** (without a body) – must be implemented by concrete subclasses.
- **Concrete methods** (with implementation) – can be inherited or overridden.
- **Fields** (instance variables) – can be `private`, `protected`, etc.
- **Constructors** – called when a subclass instance is created.
- **Access modifiers** – methods can be `public`, `protected`, `private`.

**Purpose:** Provide a common base with shared code (fields and methods) while forcing subclasses to implement specific behaviors.

**Example:**
```java
public abstract class Vehicle {
    private String brand;

    public Vehicle(String brand) {
        this.brand = brand;
    }

    public String getBrand() { return brand; }

    // Abstract method – must be implemented by subclasses
    public abstract void move();

    // Concrete method – common for all vehicles
    public void displayInfo() {
        System.out.println("Brand: " + brand);
    }
}
```

---

### 2. Interfaces

An **interface** is a contract that specifies what a class must do, but not how. It traditionally contained only abstract methods. Since Java 8, interfaces can have:

- **Default methods** (with implementation) – allow adding new methods without breaking existing implementations.
- **Static methods** – belong to the interface itself.
- **Constants** (`public static final` fields).
- **Private methods** (Java 9+) – for sharing code among default methods.

**Key Points:**
- A class can implement **multiple interfaces** (multiple inheritance of type).
- Interfaces cannot have instance fields (only `static final` constants).
- Interfaces provide **100% abstraction** (except default/static methods) – they define a contract.

**Example:**
```java
public interface Drawable {
    void draw(); // implicitly public abstract

    default void print() {
        System.out.println("Printing...");
    }
}
```

---

### 3. Key Differences

| Feature                  | Abstract Class                          | Interface                                |
|--------------------------|-----------------------------------------|------------------------------------------|
| **Keyword**              | `abstract`                              | `interface`                              |
| **Instantiation**        | Cannot be instantiated                  | Cannot be instantiated                   |
| **Fields**               | Can have instance variables              | Only `public static final` constants     |
| **Constructors**         | Yes                                     | No                                       |
| **Method Implementation**| Can have abstract and concrete methods  | Before Java 8: only abstract; Java 8+: default, static, private |
| **Access Modifiers**     | All allowed                             | Methods are implicitly `public`          |
| **Multiple Inheritance** | A class can extend only one abstract class | A class can implement multiple interfaces |
| **When to use**          | Share common code among closely related classes | Define a capability/contract for unrelated classes |

---

### 4. When to Use Abstract Class vs Interface

- **Use an abstract class when:**
  - You want to share code among several closely related classes (code reuse).
  - You expect classes that extend it to have many common methods or fields.
  - You need to declare non-public or protected members.
  - You want to provide a common base with constructors.

- **Use an interface when:**
  - You expect unrelated classes to implement your contract (e.g., `Comparable`, `Runnable`).
  - You want to specify the behavior of a particular data type, but not concerned about who implements it.
  - You need to take advantage of multiple inheritance of type.
  - You are designing a small, concise set of methods that represent a capability.

In many design patterns, you'll see a combination: an interface defines the contract, and an abstract class provides a skeletal implementation (like `AbstractList` in Java Collections).

---

### 5. Practice: Payment System with Interface

Let's design a payment system using an interface `Payment`. We'll also demonstrate how an abstract class can be used to share common logic (like logging or validation) among payment implementations.

#### Folder Structure
```
Day04-AbstractVsInterface/
├── src/
│   └── com/
│       └── example/
│           └── payment/
│               ├── Payment.java (interface)
│               ├── AbstractPayment.java (abstract class)
│               ├── CreditCardPayment.java
│               ├── PayPalPayment.java
│               └── Main.java
└── README.md (optional)
```

#### Payment.java (Interface)
```java
package com.example.payment;

public interface Payment {
    void pay(double amount);
    void refund(double amount);
    String getTransactionId();
}
```

#### AbstractPayment.java (Abstract Class)
```java
package com.example.payment;

import java.util.UUID;

public abstract class AbstractPayment implements Payment {
    private String transactionId;

    public AbstractPayment() {
        this.transactionId = generateTransactionId();
    }

    private String generateTransactionId() {
        return "TXN" + UUID.randomUUID().toString().substring(0, 8).toUpperCase();
    }

    @Override
    public String getTransactionId() {
        return transactionId;
    }

    // Common validation logic
    protected void validateAmount(double amount) {
        if (amount <= 0) {
            throw new IllegalArgumentException("Amount must be positive");
        }
    }

    // Common logging
    protected void log(String message) {
        System.out.println("[LOG] " + message);
    }
}
```

#### CreditCardPayment.java (Concrete Implementation)
```java
package com.example.payment;

public class CreditCardPayment extends AbstractPayment {
    private String cardNumber;
    private String cardHolder;

    public CreditCardPayment(String cardNumber, String cardHolder) {
        this.cardNumber = maskCardNumber(cardNumber);
        this.cardHolder = cardHolder;
    }

    private String maskCardNumber(String cardNumber) {
        if (cardNumber.length() >= 4) {
            return "**** **** **** " + cardNumber.substring(cardNumber.length() - 4);
        }
        return cardNumber;
    }

    @Override
    public void pay(double amount) {
        validateAmount(amount);
        log("Processing credit card payment of $" + amount + " for " + cardHolder + " using card " + cardNumber);
        System.out.println("✅ Payment of $" + amount + " processed successfully via Credit Card. Transaction ID: " + getTransactionId());
    }

    @Override
    public void refund(double amount) {
        validateAmount(amount);
        log("Processing refund of $" + amount + " to credit card " + cardNumber);
        System.out.println("↩️ Refund of $" + amount + " processed successfully via Credit Card.");
    }
}
```

#### PayPalPayment.java (Concrete Implementation)
```java
package com.example.payment;

public class PayPalPayment extends AbstractPayment {
    private String email;

    public PayPalPayment(String email) {
        this.email = email;
    }

    @Override
    public void pay(double amount) {
        validateAmount(amount);
        log("Processing PayPal payment of $" + amount + " for account " + email);
        System.out.println("✅ Payment of $" + amount + " processed successfully via PayPal. Transaction ID: " + getTransactionId());
    }

    @Override
    public void refund(double amount) {
        validateAmount(amount);
        log("Processing refund of $" + amount + " to PayPal account " + email);
        System.out.println("↩️ Refund of $" + amount + " processed successfully via PayPal.");
    }
}
```

#### Main.java (Demonstration)
```java
package com.example.payment;

public class Main {
    public static void main(String[] args) {
        // Using interface reference – polymorphism in action
        Payment payment1 = new CreditCardPayment("1234567890123456", "John Doe");
        Payment payment2 = new PayPalPayment("john@example.com");

        // Process payments
        processPayment(payment1, 150.00);
        System.out.println();
        processPayment(payment2, 75.50);
    }

    public static void processPayment(Payment payment, double amount) {
        payment.pay(amount);
        // Simulate a refund later
        payment.refund(amount * 0.1); // refund 10%
    }
}
```

**Expected Output:**
```
[LOG] Processing credit card payment of $150.0 for John Doe using card **** **** **** 3456
✅ Payment of $150.0 processed successfully via Credit Card. Transaction ID: TXNA1B2C3D
[LOG] Processing refund of $15.0 to credit card **** **** **** 3456
↩️ Refund of $15.0 processed successfully via Credit Card.

[LOG] Processing PayPal payment of $75.5 for account john@example.com
✅ Payment of $75.5 processed successfully via PayPal. Transaction ID: TXNE4F5G6H
[LOG] Processing refund of $7.55 to PayPal account john@example.com
↩️ Refund of $7.55 processed successfully via PayPal.
```

**Explanation:**
- `Payment` interface defines the contract.
- `AbstractPayment` provides common fields (`transactionId`), helper methods (`validateAmount`, `log`), and implements `getTransactionId()`. It's abstract because we don't want it instantiated on its own.
- `CreditCardPayment` and `PayPalPayment` extend `AbstractPayment` and implement the specific payment logic.
- In `Main`, we use `Payment` references to treat all payment types uniformly – polymorphism at work.

---

### 6. Practice Exercise

1. Add a new payment method: `CryptoPayment` (extends `AbstractPayment`) that accepts a wallet address. Implement `pay()` and `refund()`.
2. Modify `AbstractPayment` to include an abstract method `getPaymentDetails()` that returns a string. Implement it in all concrete classes.
3. Create a list of `Payment` objects and iterate through them, calling `pay(100)` for each. Observe polymorphic behavior.

---

### 7. Key Takeaways

- **Abstract classes** are for sharing code among closely related classes.
- **Interfaces** are for defining capabilities that can be implemented by any class, even unrelated ones.
- Use both together: interface for contract, abstract class for skeletal implementation (Template Method pattern).
- Polymorphism works with both – you can refer to objects by their interface or abstract class type.

Mastering abstract classes and interfaces will greatly improve your ability to design flexible, extensible systems. Tomorrow we'll combine these concepts to implement the **Strategy pattern**, which relies heavily on polymorphism and interfaces.
