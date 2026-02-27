## Day 4 — Abstract Classes vs Interfaces

### When to Use Each

- **Abstract Class**: Use when you want to share common code (fields and concrete methods) among several closely related classes. An abstract class can have constructors, instance variables, and both abstract and concrete methods. It defines a common base with some implementation, while leaving specific details to subclasses.

- **Interface**: Use to define a contract (a set of abstract methods) that unrelated classes can implement. Interfaces are ideal for declaring capabilities (e.g., `Payable`, `Runnable`, `Comparable`). Since Java 8, interfaces can also have default and static methods, but their primary role is still to specify behavior without implementation details.

**Rule of thumb**: "is-a" relationship → abstract class; "can-do" relationship → interface.

---

### Problem Statement

Design a simple payment processing system that supports multiple payment methods.

1. Create an interface **`Payment`** with a method `void processPayment(double amount)`. This defines the contract for all payment types.

2. Create an abstract class **`OnlinePayment`** that implements `Payment` and provides:
   - A field `transactionId` (String).
   - A concrete method `generateTransactionId()` to create a unique transaction ID.
   - An abstract method `validateDetails()` (to be implemented by subclasses).
   - Override `processPayment(double amount)` to call `validateDetails()` first, then print a common message like `"Processing online payment of $" + amount + " with transaction ID: " + transactionId`.

3. Create two concrete classes that extend `OnlinePayment`:
   - **`CreditCardPayment`**: overrides `validateDetails()` to simulate credit card validation.
   - **`PayPalPayment`**: overrides `validateDetails()` to simulate PayPal account validation.

4. Create another class **`CashPayment`** that directly implements the `Payment` interface (it does not inherit from `OnlinePayment`). It should implement `processPayment` by simply printing `"Processing cash payment of $" + amount`.

5. In a main class, demonstrate polymorphism by:
   - Storing different payment objects in an array of type `Payment[]`.
   - Iterating through the array and calling `processPayment(100.0)` on each.
   - Observing that the appropriate implementation is called at runtime.

---

### Explanation of the Design

- **Interface `Payment`** defines the contract that all payment types must follow. Any class that wants to be a payment method must implement `processPayment`.
- **Abstract class `OnlinePayment`** captures common features of online payments: they all need a transaction ID and some form of validation. It provides a concrete method to generate the ID and a template for `processPayment` that includes validation and a generic message. Subclasses only need to provide the specific validation logic.
- **`CashPayment`** shows that a class can implement the interface without needing the shared code from `OnlinePayment`. This demonstrates the flexibility of interfaces: unrelated classes (like cash) can still be treated as payments.
- **Polymorphism**: The array of `Payment` references can hold any object that implements the interface. The correct `processPayment` is called based on the actual object type.

---

### Java Solution

```java
import java.util.UUID;

// Interface defining the payment contract
interface Payment {
    void processPayment(double amount);
}

// Abstract class for online payments
abstract class OnlinePayment implements Payment {
    protected String transactionId;

    // Concrete method to generate a unique transaction ID
    public void generateTransactionId() {
        this.transactionId = UUID.randomUUID().toString();
    }

    // Abstract method for payment-specific validation
    protected abstract boolean validateDetails();

    // Template method that uses validation and common steps
    @Override
    public void processPayment(double amount) {
        generateTransactionId(); // set a new transaction ID
        if (validateDetails()) {
            System.out.println("Processing online payment of $" + amount +
                               " with transaction ID: " + transactionId);
        } else {
            System.out.println("Payment validation failed. Transaction cancelled.");
        }
    }
}

// Concrete class for Credit Card payments
class CreditCardPayment extends OnlinePayment {
    @Override
    protected boolean validateDetails() {
        // Simulate credit card validation
        System.out.println("Validating credit card details...");
        return true; // assume validation succeeds
    }
}

// Concrete class for PayPal payments
class PayPalPayment extends OnlinePayment {
    @Override
    protected boolean validateDetails() {
        // Simulate PayPal account validation
        System.out.println("Validating PayPal account...");
        return true; // assume validation succeeds
    }
}

// Cash payment directly implements the Payment interface
class CashPayment implements Payment {
    @Override
    public void processPayment(double amount) {
        System.out.println("Processing cash payment of $" + amount);
    }
}

// Main class to demonstrate polymorphism
public class PaymentDemo {
    public static void main(String[] args) {
        // Create an array of Payment references
        Payment[] payments = new Payment[3];
        payments[0] = new CreditCardPayment();
        payments[1] = new PayPalPayment();
        payments[2] = new CashPayment();

        // Process each payment polymorphically
        System.out.println("=== Processing Payments ===\n");
        for (Payment p : payments) {
            p.processPayment(100.0);
            System.out.println(); // blank line between payments
        }
    }
}
```

**Output:**

```
=== Processing Payments ===

Validating credit card details...
Processing online payment of $100.0 with transaction ID: 123e4567-e89b-12d3-a456-426614174000

Validating PayPal account...
Processing online payment of $100.0 with transaction ID: 987fcdeb-51a2-43f8-9abc-123456789abc

Processing cash payment of $100.0
```

*(Note: The transaction IDs will be random UUIDs each run.)*

---

### Key Takeaways

- **Abstract classes** let you share code (fields, constructors, concrete methods) among related classes. Here, `OnlinePayment` provides common fields and a template method for all online payments.
- **Interfaces** define a contract that any class (even unrelated ones) can fulfill. Here, `Payment` defines what it means to be a payment processor.
- By using both, we get the benefits of code reuse (abstract class) and polymorphism across unrelated types (interface).
- This design is flexible: you can add new payment methods (e.g., `BitcoinPayment`) without changing existing code, as long as they implement the `Payment` interface.

This example also touches on the **Template Method** pattern (in `OnlinePayment.processPayment`) and the **Strategy** pattern (different payment algorithms are interchangeable). Understanding these concepts is essential for writing maintainable, extensible software.
