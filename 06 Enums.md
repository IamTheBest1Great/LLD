## Day 6 — Enums (Used Everywhere: VehicleType, PaymentStatus, OrderStatus)

Enums (short for enumerations) are a special Java type used to define a fixed set of constants. They are incredibly useful in Low-Level Design for representing things like types, statuses, categories, and options. Enums make code more readable, type-safe, and less error-prone than using strings or integers.

---

### 1. What is an Enum?

An enum is a class that represents a group of constants (unchangeable variables, like final variables). In Java, enums are more powerful than in many other languages – they can have fields, constructors, methods, and even implement interfaces.

**Why use enums?**
- **Type safety**: You can't assign an invalid value (e.g., passing `"PENDING"` instead of `"PENDING"` – actually you can with strings, but enums prevent typos at compile time).
- **Readability**: Code like `order.setStatus(OrderStatus.CONFIRMED)` is self-documenting.
- **Centralized definition**: All allowed values are defined in one place.
- **Can have behavior**: Enums can have methods, making them more than just constants.

---

### 2. Basic Enum Definition

```java
public enum Day {
    SUNDAY, MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY, SATURDAY
}
```

Usage:
```java
Day today = Day.MONDAY;
System.out.println(today); // MONDAY
```

---

### 3. Enums with Fields, Constructors, and Methods

Enums can have fields, constructors, and methods. Each enum constant is an instance of the enum class, and you can pass parameters to the constructor.

**Example: `VehicleType` enum with description and wheels count**
```java
public enum VehicleType {
    CAR("Car", 4),
    BIKE("Bike", 2),
    TRUCK("Truck", 6),
    BUS("Bus", 6);

    private final String displayName;
    private final int wheels;

    // Constructor – called once per constant
    VehicleType(String displayName, int wheels) {
        this.displayName = displayName;
        this.wheels = wheels;
    }

    public String getDisplayName() {
        return displayName;
    }

    public int getWheels() {
        return wheels;
    }

    @Override
    public String toString() {
        return displayName;
    }
}
```

**Usage:**
```java
VehicleType type = VehicleType.CAR;
System.out.println(type.getDisplayName()); // Car
System.out.println(type.getWheels());      // 4
System.out.println(type);                  // Car (because toString is overridden)
```

---

### 4. Important Enum Methods

All enums implicitly extend `java.lang.Enum` and have these useful methods:

- `name()` – returns the exact string used to declare the enum constant.
- `ordinal()` – returns the position (0-based) of the constant in the enum declaration.
- `valueOf(Class<T> enumType, String name)` – returns the enum constant with the specified name.
- `values()` – returns an array of all enum constants (implicitly added by compiler).

```java
VehicleType[] types = VehicleType.values();
for (VehicleType vt : types) {
    System.out.println(vt.name() + " : " + vt.ordinal());
}
// Output:
// CAR : 0
// BIKE : 1
// TRUCK : 2
// BUS : 3
```

**Parsing from string:**
```java
VehicleType type = VehicleType.valueOf("CAR"); // returns VehicleType.CAR
// Throws IllegalArgumentException if name doesn't match
```

---

### 5. Using Enums in Switch Statements

Enums work beautifully with switch statements.

```java
public class ParkingLot {
    public double calculateFee(VehicleType type, int hours) {
        switch (type) {
            case CAR:
                return hours * 10.0;
            case BIKE:
                return hours * 5.0;
            case TRUCK:
            case BUS:
                return hours * 20.0;
            default:
                throw new IllegalArgumentException("Unknown type");
        }
    }
}
```

---

### 6. Enums with Abstract Methods (Strategy-like Behavior)

You can define abstract methods in an enum and override them per constant. This is useful when behavior varies per constant.

**Example: `PaymentStatus` with next status transition**
```java
public enum PaymentStatus {
    PENDING {
        @Override
        public PaymentStatus next() {
            return COMPLETED;
        }
    },
    COMPLETED {
        @Override
        public PaymentStatus next() {
            return REFUNDED;
        }
    },
    REFUNDED {
        @Override
        public PaymentStatus next() {
            return REFUNDED; // terminal state
        }
    },
    FAILED {
        @Override
        public PaymentStatus next() {
            return FAILED; // terminal
        }
    };

    public abstract PaymentStatus next();
}
```

**Usage:**
```java
PaymentStatus status = PaymentStatus.PENDING;
System.out.println(status.next()); // COMPLETED
System.out.println(status.next().next()); // REFUNDED
```

This is similar to the State pattern but implemented in an enum. For simple state machines, enums with methods are very handy.

---

### 7. Enums Implementing Interfaces

Enums can implement interfaces, allowing them to be used polymorphically.

**Example: `OrderStatus` implementing a `Cancellable` interface**
```java
interface Cancellable {
    boolean canCancel();
}

public enum OrderStatus implements Cancellable {
    NEW {
        @Override
        public boolean canCancel() {
            return true;
        }
    },
    CONFIRMED {
        @Override
        public boolean canCancel() {
            return true;
        }
    },
    SHIPPED {
        @Override
        public boolean canCancel() {
            return false;
        }
    },
    DELIVERED {
        @Override
        public boolean canCancel() {
            return false;
        }
    },
    CANCELLED {
        @Override
        public boolean canCancel() {
            return false;
        }
    };

    // You can also provide a default implementation
    // public boolean canCancel() { return false; }
}
```

**Usage:**
```java
OrderStatus status = OrderStatus.NEW;
if (status.canCancel()) {
    System.out.println("Order can be cancelled.");
}
```

---

### 8. EnumSet and EnumMap

Java provides specialized collections for enums: `EnumSet` and `EnumMap`. They are highly efficient.

- **EnumSet**: A `Set` implementation for enums. All elements must come from a single enum type.
- **EnumMap**: A `Map` implementation where keys are enums.

**Example:**
```java
// EnumSet
EnumSet<VehicleType> heavyVehicles = EnumSet.of(VehicleType.TRUCK, VehicleType.BUS);
if (heavyVehicles.contains(VehicleType.TRUCK)) {
    System.out.println("Truck is a heavy vehicle.");
}

// EnumMap
EnumMap<VehicleType, Integer> parkingSpots = new EnumMap<>(VehicleType.class);
parkingSpots.put(VehicleType.CAR, 100);
parkingSpots.put(VehicleType.BIKE, 50);
int carSpots = parkingSpots.get(VehicleType.CAR);
```

---

### 9. Practical LLD Examples

Let's see how enums are used in typical LLD problems.

#### a) Parking Lot System – VehicleType and PaymentStatus

```java
public enum VehicleType {
    CAR, BIKE, TRUCK
}

public enum PaymentStatus {
    PENDING, COMPLETED, FAILED, REFUNDED
}

public class ParkingTicket {
    private String ticketId;
    private VehicleType vehicleType;
    private long entryTime;
    private PaymentStatus paymentStatus;

    // constructor, getters, setters
}
```

#### b) Order Management – OrderStatus

```java
public enum OrderStatus {
    PLACED, CONFIRMED, PROCESSING, SHIPPED, DELIVERED, CANCELLED
}

public class Order {
    private int orderId;
    private OrderStatus status;
    // ...
}
```

#### c) Vending Machine – State as Enum (simpler alternative to State pattern)

```java
public enum VendingMachineState {
    IDLE, SELECTING, DISPENSING, OUT_OF_ORDER
}

public class VendingMachine {
    private VendingMachineState state;
    // ...
}
```

---

### 10. File Structure for Demo Project

Let's create a small project demonstrating enums with `VehicleType`, `PaymentStatus`, and `OrderStatus`. We'll include a main class to show usage.

```
Day06-Enums/
├── src/
│   └── com/
│       └── example/
│           └── enumsdemo/
│               ├── VehicleType.java
│               ├── PaymentStatus.java
│               ├── OrderStatus.java
│               ├── ParkingTicket.java
│               ├── Order.java
│               └── Main.java
└── README.md
```

#### VehicleType.java
```java
package com.example.enumsdemo;

public enum VehicleType {
    CAR("Car", 4),
    BIKE("Bike", 2),
    TRUCK("Truck", 6),
    BUS("Bus", 6);

    private final String displayName;
    private final int wheels;

    VehicleType(String displayName, int wheels) {
        this.displayName = displayName;
        this.wheels = wheels;
    }

    public String getDisplayName() { return displayName; }
    public int getWheels() { return wheels; }

    @Override
    public String toString() { return displayName; }
}
```

#### PaymentStatus.java
```java
package com.example.enumsdemo;

public enum PaymentStatus {
    PENDING {
        @Override
        public PaymentStatus next() {
            return COMPLETED;
        }
    },
    COMPLETED {
        @Override
        public PaymentStatus next() {
            return REFUNDED;
        }
    },
    REFUNDED {
        @Override
        public PaymentStatus next() {
            return REFUNDED;
        }
    },
    FAILED {
        @Override
        public PaymentStatus next() {
            return FAILED;
        }
    };

    public abstract PaymentStatus next();
}
```

#### OrderStatus.java
```java
package com.example.enumsdemo;

interface Cancellable {
    boolean canCancel();
}

public enum OrderStatus implements Cancellable {
    PLACED {
        @Override
        public boolean canCancel() { return true; }
    },
    CONFIRMED {
        @Override
        public boolean canCancel() { return true; }
    },
    PROCESSING {
        @Override
        public boolean canCancel() { return false; }
    },
    SHIPPED {
        @Override
        public boolean canCancel() { return false; }
    },
    DELIVERED {
        @Override
        public boolean canCancel() { return false; }
    },
    CANCELLED {
        @Override
        public boolean canCancel() { return false; }
    };
}
```

#### ParkingTicket.java
```java
package com.example.enumsdemo;

import java.time.Instant;

public class ParkingTicket {
    private String ticketId;
    private VehicleType vehicleType;
    private Instant entryTime;
    private PaymentStatus paymentStatus;

    public ParkingTicket(String ticketId, VehicleType vehicleType) {
        this.ticketId = ticketId;
        this.vehicleType = vehicleType;
        this.entryTime = Instant.now();
        this.paymentStatus = PaymentStatus.PENDING;
    }

    // getters and setters
    public String getTicketId() { return ticketId; }
    public VehicleType getVehicleType() { return vehicleType; }
    public Instant getEntryTime() { return entryTime; }
    public PaymentStatus getPaymentStatus() { return paymentStatus; }
    public void setPaymentStatus(PaymentStatus paymentStatus) { this.paymentStatus = paymentStatus; }

    @Override
    public String toString() {
        return "ParkingTicket{" +
                "ticketId='" + ticketId + '\'' +
                ", vehicleType=" + vehicleType +
                ", entryTime=" + entryTime +
                ", paymentStatus=" + paymentStatus +
                '}';
    }
}
```

#### Order.java
```java
package com.example.enumsdemo;

public class Order {
    private int orderId;
    private OrderStatus status;

    public Order(int orderId) {
        this.orderId = orderId;
        this.status = OrderStatus.PLACED;
    }

    public int getOrderId() { return orderId; }
    public OrderStatus getStatus() { return status; }
    public void setStatus(OrderStatus status) { this.status = status; }

    public boolean canCancel() {
        return status.canCancel();
    }

    @Override
    public String toString() {
        return "Order{orderId=" + orderId + ", status=" + status + '}';
    }
}
```

#### Main.java
```java
package com.example.enumsdemo;

public class Main {
    public static void main(String[] args) {
        // VehicleType demo
        System.out.println("=== VehicleType Demo ===");
        for (VehicleType vt : VehicleType.values()) {
            System.out.println(vt + " has " + vt.getWheels() + " wheels.");
        }

        // PaymentStatus demo with next()
        System.out.println("\n=== PaymentStatus Demo ===");
        PaymentStatus status = PaymentStatus.PENDING;
        System.out.println("Current: " + status);
        System.out.println("Next: " + status.next());
        System.out.println("Next after next: " + status.next().next());

        // Order demo with canCancel()
        System.out.println("\n=== Order Demo ===");
        Order order = new Order(1001);
        System.out.println(order);
        System.out.println("Can cancel? " + order.canCancel());

        order.setStatus(OrderStatus.PROCESSING);
        System.out.println(order);
        System.out.println("Can cancel? " + order.canCancel());

        // ParkingTicket demo
        System.out.println("\n=== ParkingTicket Demo ===");
        ParkingTicket ticket = new ParkingTicket("T123", VehicleType.CAR);
        System.out.println(ticket);
        ticket.setPaymentStatus(PaymentStatus.COMPLETED);
        System.out.println("After payment: " + ticket.getPaymentStatus());
    }
}
```

**Expected Output:**
```
=== VehicleType Demo ===
Car has 4 wheels.
Bike has 2 wheels.
Truck has 6 wheels.
Bus has 6 wheels.

=== PaymentStatus Demo ===
Current: PENDING
Next: COMPLETED
Next after next: REFUNDED

=== Order Demo ===
Order{orderId=1001, status=PLACED}
Can cancel? true
Order{orderId=1001, status=PROCESSING}
Can cancel? false

=== ParkingTicket Demo ===
ParkingTicket{ticketId='T123', vehicleType=Car, entryTime=2025-02-27T10:00:00Z, paymentStatus=PENDING}
After payment: COMPLETED
```

---

### 11. Practice Exercise

1. Create an enum `WeekDay` with constants MONDAY through SUNDAY. Add a method `isWeekend()` that returns true for SATURDAY and SUNDAY.
2. Create an enum `TrafficLight` with constants RED, YELLOW, GREEN. Each should have a `next()` method that returns the next light in cycle (RED → GREEN → YELLOW → RED).
3. In a `RestaurantOrder` system, create an enum `OrderStatus` with states: RECEIVED, PREPARING, READY, DELIVERED, CANCELLED. Add a method `canModify()` that returns true only for RECEIVED.
4. Use `EnumMap` to store the number of available parking spots for each `VehicleType`. Write a method to print the spots.

---

### 12. Key Takeaways

- Enums are type-safe and more expressive than integer or string constants.
- Enums can have fields, constructors, and methods, making them mini-classes.
- Enums can implement interfaces and have abstract methods, enabling polymorphic behavior.
- Use `EnumSet` and `EnumMap` for efficient collections.
- In LLD, enums are perfect for representing types, statuses, categories, and even simple state machines.
- Always prefer enums over `public static final int` constants.

Mastering enums will make your designs cleaner and more robust. Tomorrow we'll start applying all these concepts to implement our first design pattern – the **Strategy Pattern**!
