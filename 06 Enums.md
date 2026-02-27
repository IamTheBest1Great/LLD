## Day 6 — Enums

Enums (enumerations) are a special data type in Java that define a fixed set of constants. They are used extensively in low-level design to represent a fixed collection of related values, such as vehicle types, payment statuses, order statuses, days of the week, etc.

### Why Use Enums?

- **Type safety**: You cannot assign a value outside the predefined set.
- **Readability**: Code becomes self‑documenting (`OrderStatus.PAID` is clearer than `"PAID"` or `1`).
- **Compile‑time checking**: The compiler catches invalid values.
- **Rich behavior**: Enums can have fields, constructors, and methods, allowing you to associate additional data with each constant.

### Common Use Cases

- `VehicleType` – CAR, BIKE, TRUCK
- `PaymentStatus` – PENDING, PAID, FAILED, REFUNDED
- `OrderStatus` – NEW, PROCESSING, SHIPPED, DELIVERED, CANCELLED
- `DayOfWeek` – MONDAY, TUESDAY, …
- `Direction` – NORTH, SOUTH, EAST, WEST

### Problem Statement

Design a simple order processing system that uses enums to represent order status and payment status. Additionally, use an enum for vehicle types to categorize orders based on the type of vehicle involved.

1. Create an enum **`OrderStatus`** with constants: `NEW`, `PROCESSING`, `SHIPPED`, `DELIVERED`, `CANCELLED`. Each constant should have a display name (e.g., "New Order", "Processing", etc.) and a method `canCancel()` that returns `true` if the order can be cancelled (i.e., only `NEW` and `PROCESSING` are cancellable).

2. Create an enum **`PaymentStatus`** with constants: `PENDING`, `PAID`, `FAILED`. Each constant should have a display name and a method `isSuccessful()` that returns `true` only for `PAID`.

3. Create an enum **`VehicleType`** with constants: `CAR`, `BIKE`, `TRUCK`. Each constant should have a `maxSpeed` value (in km/h) associated: CAR = 200, BIKE = 120, TRUCK = 140.

4. Create a class **`Order`** that has:
   - Fields: `orderId` (int), `vehicleType` (VehicleType), `status` (OrderStatus), `paymentStatus` (PaymentStatus).
   - Constructor to initialize all fields (default status = NEW, paymentStatus = PENDING).
   - Methods to update status and payment status (with validation, e.g., cannot mark as PAID if already PAID, cannot ship if not PAID).
   - Override `toString()` to display order details.

5. In a main class, create a few orders, simulate status changes, and demonstrate enum usage.

### Java Solution

```java
// Enum for OrderStatus with display name and cancel check
enum OrderStatus {
    NEW("New Order"),
    PROCESSING("Processing"),
    SHIPPED("Shipped"),
    DELIVERED("Delivered"),
    CANCELLED("Cancelled");

    private final String displayName;

    OrderStatus(String displayName) {
        this.displayName = displayName;
    }

    public String getDisplayName() {
        return displayName;
    }

    public boolean canCancel() {
        return this == NEW || this == PROCESSING;
    }
}

// Enum for PaymentStatus with display name and success check
enum PaymentStatus {
    PENDING("Pending"),
    PAID("Paid"),
    FAILED("Failed");

    private final String displayName;

    PaymentStatus(String displayName) {
        this.displayName = displayName;
    }

    public String getDisplayName() {
        return displayName;
    }

    public boolean isSuccessful() {
        return this == PAID;
    }
}

// Enum for VehicleType with max speed
enum VehicleType {
    CAR(200),
    BIKE(120),
    TRUCK(140);

    private final int maxSpeed;

    VehicleType(int maxSpeed) {
        this.maxSpeed = maxSpeed;
    }

    public int getMaxSpeed() {
        return maxSpeed;
    }
}

// Order class
class Order {
    private final int orderId;
    private VehicleType vehicleType;
    private OrderStatus status;
    private PaymentStatus paymentStatus;

    public Order(int orderId, VehicleType vehicleType) {
        this.orderId = orderId;
        this.vehicleType = vehicleType;
        this.status = OrderStatus.NEW;           // default
        this.paymentStatus = PaymentStatus.PENDING; // default
    }

    // Update order status with validation
    public void updateStatus(OrderStatus newStatus) {
        // Example validations
        if (this.status == OrderStatus.CANCELLED) {
            System.out.println("Order already cancelled. No further updates.");
            return;
        }
        if (this.status == OrderStatus.DELIVERED) {
            System.out.println("Order already delivered. Cannot change status.");
            return;
        }
        // Can't ship if payment not successful
        if (newStatus == OrderStatus.SHIPPED && !paymentStatus.isSuccessful()) {
            System.out.println("Cannot ship order: payment not successful.");
            return;
        }
        this.status = newStatus;
        System.out.println("Order " + orderId + " status updated to " + status.getDisplayName());
    }

    // Update payment status
    public void updatePaymentStatus(PaymentStatus newPaymentStatus) {
        if (this.paymentStatus == PaymentStatus.PAID) {
            System.out.println("Payment already completed. Cannot change.");
            return;
        }
        this.paymentStatus = newPaymentStatus;
        System.out.println("Order " + orderId + " payment status: " + paymentStatus.getDisplayName());
    }

    // Cancel order if allowed
    public void cancelOrder() {
        if (status.canCancel()) {
            this.status = OrderStatus.CANCELLED;
            System.out.println("Order " + orderId + " cancelled.");
        } else {
            System.out.println("Order " + orderId + " cannot be cancelled at " + status.getDisplayName() + " stage.");
        }
    }

    @Override
    public String toString() {
        return String.format("Order #%d [Vehicle: %s, Max Speed: %d km/h, Status: %s, Payment: %s]",
                orderId, vehicleType, vehicleType.getMaxSpeed(),
                status.getDisplayName(), paymentStatus.getDisplayName());
    }
}

// Demo class
public class EnumDemo {
    public static void main(String[] args) {
        // Create orders
        Order order1 = new Order(1001, VehicleType.CAR);
        Order order2 = new Order(1002, VehicleType.BIKE);
        Order order3 = new Order(1003, VehicleType.TRUCK);

        // Display initial orders
        System.out.println("=== Initial Orders ===");
        System.out.println(order1);
        System.out.println(order2);
        System.out.println(order3);
        System.out.println();

        // Simulate workflow
        order1.updatePaymentStatus(PaymentStatus.PAID);
        order1.updateStatus(OrderStatus.PROCESSING);
        order1.updateStatus(OrderStatus.SHIPPED);
        order1.updateStatus(OrderStatus.DELIVERED);
        System.out.println(order1);
        System.out.println();

        order2.cancelOrder();   // NEW can cancel
        System.out.println(order2);
        System.out.println();

        order3.updateStatus(OrderStatus.SHIPPED); // Should fail because payment not paid
        order3.updatePaymentStatus(PaymentStatus.PAID);
        order3.updateStatus(OrderStatus.SHIPPED);
        order3.cancelOrder();   // SHIPPED cannot cancel
        System.out.println(order3);
    }
}
```

**Output:**

```
=== Initial Orders ===
Order #1001 [Vehicle: CAR, Max Speed: 200 km/h, Status: New Order, Payment: Pending]
Order #1002 [Vehicle: BIKE, Max Speed: 120 km/h, Status: New Order, Payment: Pending]
Order #1003 [Vehicle: TRUCK, Max Speed: 140 km/h, Status: New Order, Payment: Pending]

Order 1001 payment status: Paid
Order 1001 status updated to Processing
Order 1001 status updated to Shipped
Order 1001 status updated to Delivered
Order #1001 [Vehicle: CAR, Max Speed: 200 km/h, Status: Delivered, Payment: Paid]

Order 1002 cancelled.
Order #1002 [Vehicle: BIKE, Max Speed: 120 km/h, Status: Cancelled, Payment: Pending]

Cannot ship order: payment not successful.
Order 1003 payment status: Paid
Order 1003 status updated to Shipped
Order 1003 cannot be cancelled at Shipped stage.
Order #1003 [Vehicle: TRUCK, Max Speed: 140 km/h, Status: Shipped, Payment: Paid]
```

### Key Takeaways

- Enums provide **type safety** – you cannot accidentally assign an invalid status.
- They can **encapsulate behavior** (e.g., `canCancel()`, `isSuccessful()`) and **data** (e.g., display name, max speed).
- Using enums makes code more readable and maintainable, especially when dealing with fixed sets of constants.
- Enums are widely used in real‑world applications to represent states, categories, options, and more.

This example demonstrates how enums can be more than just a list of constants – they become powerful, self‑describing components of your design.
