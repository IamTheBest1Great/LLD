## Day 5 — Composition vs Inheritance (MOST IMPORTANT CONCEPT IN LLD)

Today we tackle one of the most critical decisions in object-oriented design: **whether to use inheritance or composition**. This choice impacts flexibility, maintainability, and scalability of your code. The mantra *"Favor composition over inheritance"* is a cornerstone of many design patterns (like Strategy, Decorator, etc.). Let's understand why.

---

### 1. Definitions

- **Inheritance** (`IS-A` relationship): A class (subclass) derives from another class (superclass), inheriting its fields and methods. Example: `Dog extends Animal`.
- **Composition** (`HAS-A` relationship): A class contains references to objects of other classes as members. Example: `Car` has an `Engine` object.

Both allow code reuse, but they achieve it differently.

---

### 2. Inheritance – The Good and The Bad

**Good:**
- Straightforward when a genuine `IS-A` relationship exists.
- Code reuse – common logic in a base class.
- Polymorphism – you can treat a subclass as its superclass.
- Easy to override or extend behavior.

**Bad:**
- **Tight coupling**: Subclass depends on implementation details of superclass. Changes in superclass can break subclasses (fragile base class problem).
- **Inheritance hierarchy can become deep and complex**.
- **Limited flexibility**: A subclass can only extend one class (single inheritance in Java). This restricts design.
- **Breaks encapsulation**: Subclasses may need to access protected members, exposing internal details.
- **Not always semantically correct**: Many relationships are not `IS-A` but `HAS-A`. For example, a `Car` is not an `Engine`; it has an engine.

---

### 3. Composition – The Good and The Bad

**Good:**
- **Loose coupling**: Class only knows about the interfaces of its components, not their concrete implementations (especially if you program to interfaces).
- **Flexibility**: You can replace components at runtime (e.g., swap engine types) – this is the basis of Strategy pattern.
- **Reuse through delegation**: Use functionality of composed objects without inheriting from them.
- **Easier to test**: You can mock components.
- **Follows Single Responsibility Principle**: Each class focuses on one thing, composing others for additional behavior.

**Bad:**
- **More boilerplate**: You need to write forwarding methods if you want to expose component methods.
- **Harder to see the big picture**: The system is composed of many small objects, which may increase complexity if overused.

---

### 4. Favor Composition Over Inheritance

This design principle suggests that you should prefer composing objects to achieve polymorphic behavior and code reuse rather than relying on inheritance hierarchies. Inheritance should be used only when a genuine `IS-A` relationship exists and when the superclass is stable and designed for extension.

**Why?**
- Composition leads to more flexible, maintainable, and testable code.
- It aligns with the Open/Closed Principle (open for extension, closed for modification) because you can add new behavior by plugging in new components without changing existing code.
- Many design patterns (Strategy, Decorator, Composite, etc.) are based on composition.

---

### 5. Classic Bad Example: `Car extends Engine`

Let's see why this is wrong.

**Bad Design (Inheritance)**
```java
class Engine {
    public void start() {
        System.out.println("Engine starting...");
    }
    public void stop() {
        System.out.println("Engine stopping...");
    }
}

class Car extends Engine {  // Car IS-A Engine? No!
    private String model;

    public void drive() {
        start();   // inherited from Engine
        System.out.println("Car is moving");
    }
}
```

**Why is this bad?**
- Semantic violation: A car is not an engine. It has an engine.
- If you later add a `Motorcycle` that also extends `Engine`, you duplicate the problem.
- `Car` cannot have multiple engines (e.g., hybrid with two engines) because it's limited to one superclass.
- If `Engine` changes (e.g., adds a `dispose()` method), all subclasses are affected, even if they don't need it.

---

### 6. Good Example: `Car has Engine` (Composition)

**Good Design (Composition)**
```java
class Engine {
    private String type;

    public Engine(String type) {
        this.type = type;
    }

    public void start() {
        System.out.println(type + " engine starting...");
    }

    public void stop() {
        System.out.println(type + " engine stopping...");
    }
}

class Car {
    private String model;
    private Engine engine;  // Composition: Car HAS-A Engine

    public Car(String model, Engine engine) {
        this.model = model;
        this.engine = engine;
    }

    public void drive() {
        engine.start();     // Delegation
        System.out.println(model + " is moving");
    }

    public void setEngine(Engine engine) { // Allows swapping engine at runtime
        this.engine = engine;
    }
}
```

**Benefits:**
- Clear `HAS-A` relationship.
- `Car` can have any type of engine (Petrol, Diesel, Electric) by passing different `Engine` objects.
- You can change the engine at runtime (`setEngine`).
- `Engine` can be reused in other classes (e.g., `Boat`).
- Each class has a single responsibility.

---

### 7. Using Interfaces to Enhance Composition

To make composition even more flexible, we can depend on abstractions (interfaces) rather than concrete classes.

```java
interface Engine {
    void start();
    void stop();
}

class PetrolEngine implements Engine {
    @Override
    public void start() { System.out.println("Petrol engine starting..."); }
    @Override
    public void stop() { System.out.println("Petrol engine stopping..."); }
}

class ElectricEngine implements Engine {
    @Override
    public void start() { System.out.println("Electric motor humming..."); }
    @Override
    public void stop() { System.out.println("Electric motor stopping..."); }
}

class Car {
    private Engine engine;  // Depends on abstraction
    // constructor, drive method, etc.
}
```

Now you can inject any `Engine` implementation into `Car` – this is **dependency injection** and follows the Dependency Inversion Principle.

---

### 8. When to Use Inheritance?

Inheritance is appropriate when:
- There is a clear **IS-A** relationship (e.g., `Dog` is an `Animal`).
- The superclass is designed for extension (e.g., abstract classes with protected methods).
- You need to leverage polymorphism in a way that is stable (e.g., `ArrayList` extends `AbstractList`).
- The hierarchy is shallow and unlikely to change.

**Example of good inheritance:**
```java
abstract class Bird {
    public abstract void fly();
}

class Sparrow extends Bird {
    @Override
    public void fly() {
        System.out.println("Sparrow flying");
    }
}
```

But even here, if you later want to model a `Penguin` (which cannot fly), inheritance fails. Composition would be better: `Bird` has a `FlyingBehavior` (Strategy pattern).

---

### 9. Practice: Design a System Using Composition

Let's build a simple vehicle system using composition. We'll have `Engine`, `Transmission`, and `FuelTank` components, and a `Vehicle` class that composes them.

#### Folder Structure
```
Day05-CompositionVsInheritance/
├── src/
│   └── com/
│       └── example/
│           └── vehicle/
│               ├── Engine.java (interface)
│               ├── PetrolEngine.java
│               ├── ElectricEngine.java
│               ├── Transmission.java (interface)
│               ├── ManualTransmission.java
│               ├── AutomaticTransmission.java
│               ├── FuelTank.java
│               ├── Vehicle.java
│               └── Main.java
└── README.md (optional)
```

#### Engine.java (interface)
```java
package com.example.vehicle;

public interface Engine {
    void start();
    void stop();
}
```

#### PetrolEngine.java
```java
package com.example.vehicle;

public class PetrolEngine implements Engine {
    @Override
    public void start() {
        System.out.println("Petrol engine roars to life!");
    }

    @Override
    public void stop() {
        System.out.println("Petrol engine sputters and stops.");
    }
}
```

#### ElectricEngine.java
```java
package com.example.vehicle;

public class ElectricEngine implements Engine {
    @Override
    public void start() {
        System.out.println("Electric motor hums quietly.");
    }

    @Override
    public void stop() {
        System.out.println("Electric motor goes silent.");
    }
}
```

#### Transmission.java (interface)
```java
package com.example.vehicle;

public interface Transmission {
    void shiftGear(int gear);
}
```

#### ManualTransmission.java
```java
package com.example.vehicle;

public class ManualTransmission implements Transmission {
    @Override
    public void shiftGear(int gear) {
        System.out.println("Shifting manual transmission to gear " + gear);
    }
}
```

#### AutomaticTransmission.java
```java
package com.example.vehicle;

public class AutomaticTransmission implements Transmission {
    @Override
    public void shiftGear(int gear) {
        System.out.println("Automatic transmission smoothly changes to gear " + gear);
    }
}
```

#### FuelTank.java (simple class)
```java
package com.example.vehicle;

public class FuelTank {
    private int fuelLevel; // percentage

    public FuelTank(int initialLevel) {
        this.fuelLevel = initialLevel;
    }

    public void consumeFuel(int amount) {
        if (fuelLevel >= amount) {
            fuelLevel -= amount;
            System.out.println("Fuel consumed. Remaining: " + fuelLevel + "%");
        } else {
            System.out.println("Not enough fuel!");
        }
    }

    public int getFuelLevel() { return fuelLevel; }
}
```

#### Vehicle.java (composes all parts)
```java
package com.example.vehicle;

public class Vehicle {
    private String model;
    private Engine engine;
    private Transmission transmission;
    private FuelTank fuelTank;

    public Vehicle(String model, Engine engine, Transmission transmission, FuelTank fuelTank) {
        this.model = model;
        this.engine = engine;
        this.transmission = transmission;
        this.fuelTank = fuelTank;
    }

    public void start() {
        if (fuelTank.getFuelLevel() > 0) {
            engine.start();
        } else {
            System.out.println("Cannot start: no fuel!");
        }
    }

    public void stop() {
        engine.stop();
    }

    public void drive() {
        start();
        transmission.shiftGear(1);
        System.out.println(model + " is moving...");
        fuelTank.consumeFuel(10);
        stop();
    }

    // Setters to swap components at runtime
    public void setEngine(Engine engine) { this.engine = engine; }
    public void setTransmission(Transmission transmission) { this.transmission = transmission; }
}
```

#### Main.java
```java
package com.example.vehicle;

public class Main {
    public static void main(String[] args) {
        // Build a petrol car with manual transmission
        Engine petrol = new PetrolEngine();
        Transmission manual = new ManualTransmission();
        FuelTank tank = new FuelTank(100);

        Vehicle myCar = new Vehicle("Toyota Corolla", petrol, manual, tank);
        myCar.drive();

        System.out.println("\n--- Swapping to electric engine and automatic transmission ---");
        // Swap engine and transmission at runtime
        myCar.setEngine(new ElectricEngine());
        myCar.setTransmission(new AutomaticTransmission());
        myCar.drive();
    }
}
```

**Output:**
```
Petrol engine roars to life!
Shifting manual transmission to gear 1
Toyota Corolla is moving...
Fuel consumed. Remaining: 90%
Petrol engine sputters and stops.

--- Swapping to electric engine and automatic transmission ---
Electric motor hums quietly.
Automatic transmission smoothly changes to gear 1
Toyota Corolla is moving...
Fuel consumed. Remaining: 80%
Electric motor goes silent.
```

**Observations:**
- `Vehicle` is composed of interchangeable parts. We can change engine or transmission at runtime without modifying `Vehicle` code.
- This design is extensible: we can add a `HydrogenEngine` later without touching existing classes (just implement `Engine`).
- Each component has a single responsibility.

---

### 10. Practice Exercise

1. Add a new engine type: `DieselEngine` (implements `Engine`).
2. Add a new transmission type: `CVTTransmission` (implements `Transmission`).
3. Create a `Motorcycle` class that also uses composition with `Engine` and `Transmission` (no fuel tank? maybe a simpler version).
4. Modify `Vehicle` to include a `List<Tire>` (composition with a collection). Tire class could have pressure, etc.
5. Think of a scenario where inheritance would be more appropriate than composition (e.g., `SavingsAccount` extends `BankAccount`). Implement it and explain your reasoning.

---

### 11. Key Takeaways

- **Favor composition over inheritance** is a principle, not an absolute rule. Use composition when you need flexibility and low coupling.
- Composition models **HAS-A** relationships; inheritance models **IS-A**.
- Composition allows behavior to be changed at runtime (via setter injection) and promotes code reuse through delegation.
- Inheritance is appropriate for stable, genuinely hierarchical relationships, but avoid deep hierarchies.
- Design patterns like Strategy, Decorator, and Composite are based on composition.

Mastering this concept will dramatically improve your LLD skills. Tomorrow we'll apply composition to implement the **Strategy pattern**, which we've been building toward all week.
