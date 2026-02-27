## Day 5 — Composition vs Inheritance

### The Most Important Concept in Low-Level Design

In object-oriented design, two fundamental relationships exist between classes:

- **Inheritance** (IS‑A relationship): A `Car` **is a** `Vehicle`.  
- **Composition** (HAS‑A relationship): A `Car` **has an** `Engine`, **has** `Wheels`, etc.

While inheritance promotes code reuse, it can lead to rigid designs if overused. The **Gang of Four** principle *"Favor composition over inheritance"* reminds us that composition often yields more flexible, maintainable systems.

### Why "Car extends Engine" is Bad

If you write `class Car extends Engine`, you are saying a Car *is an* Engine – which makes little sense conceptually. This misuse of inheritance creates:

- **Tight coupling**: Changes in `Engine` may unintentionally affect `Car`.
- **Fragile base class**: Modifying the base class can break subclasses.
- **Inflexibility**: A car cannot easily change its engine type at runtime.
- **Poor abstraction**: The Car inherits all Engine methods, even those irrelevant (e.g., `Engine.getCylinders()` doesn't belong to a Car).

### "Car has Engine" is Good (Composition)

With composition, a Car contains references to its parts (Engine, Wheels, etc.). This models reality: a car *has* an engine, but it is not an engine itself. Benefits:

- **Loose coupling**: Car depends on abstractions (interfaces), not concrete classes.
- **Flexibility**: You can swap the engine at runtime (e.g., replace a `PetrolEngine` with an `ElectricEngine`).
- **Better encapsulation**: Car exposes only behavior relevant to a car.
- **Testability**: You can easily mock the engine for unit tests.

### Problem Statement

Design a simple car system to illustrate composition over inheritance.

1. **Create an interface `Engine`** with a method `void start()`.
2. **Implement two concrete engines**:
   - `PetrolEngine`: prints `"Petrol engine started."`
   - `ElectricEngine`: prints `"Electric engine started (silent)."`
3. **Create a `Car` class** that uses composition:
   - It has a field of type `Engine` (the engine).
   - It has a constructor that accepts an `Engine` (dependency injection).
   - It has a method `startCar()` that delegates to the engine's `start()` method.
   - Optionally, add methods like `setEngine(Engine engine)` to allow runtime replacement.
4. In a main class, demonstrate:
   - Creating a Car with a `PetrolEngine`, starting it.
   - Creating another Car with an `ElectricEngine`, starting it.
   - (Optional) Changing the engine of an existing car.

### Java Solution

```java
// Engine interface (abstraction)
interface Engine {
    void start();
}

// Concrete engine types
class PetrolEngine implements Engine {
    @Override
    public void start() {
        System.out.println("Petrol engine started.");
    }
}

class ElectricEngine implements Engine {
    @Override
    public void start() {
        System.out.println("Electric engine started (silent).");
    }
}

// Car uses composition: it HAS an Engine
class Car {
    private Engine engine;   // composition

    // Constructor injection
    public Car(Engine engine) {
        this.engine = engine;
    }

    // Delegate to the engine
    public void startCar() {
        engine.start();
    }

    // Optionally, allow changing engine at runtime
    public void setEngine(Engine engine) {
        System.out.println("Swapping engine...");
        this.engine = engine;
    }
}

// Demo class
public class CompositionDemo {
    public static void main(String[] args) {
        // Create a car with a petrol engine
        Car petrolCar = new Car(new PetrolEngine());
        petrolCar.startCar();

        // Create a car with an electric engine
        Car electricCar = new Car(new ElectricEngine());
        electricCar.startCar();

        // Swap engine on the petrol car (make it hybrid? :)
        petrolCar.setEngine(new ElectricEngine());
        petrolCar.startCar();
    }
}
```

**Output:**
```
Petrol engine started.
Electric engine started (silent).
Swapping engine...
Electric engine started (silent).
```

### Why This is Better Than Inheritance

- **No forced IS‑A relationship**: `Car` is not an `Engine`, it simply uses one.
- **Runtime flexibility**: We can change the engine of a `Car` object dynamically.
- **Open/Closed Principle**: Adding a new engine type (e.g., `HybridEngine`) requires no changes to `Car` – just implement the `Engine` interface.
- **Testability**: You can pass a mock engine to `Car` during testing.

### Key Takeaways

- **Inheritance** should be used for genuine IS‑A relationships (e.g., `Dog extends Animal`).
- **Composition** (HAS‑A) is preferred for most other cases, especially when behavior can vary or needs to be changed at runtime.
- By depending on interfaces (abstractions) rather than concrete classes, you create loosely coupled, maintainable designs.
- This principle is at the heart of many design patterns: Strategy, Decorator, Bridge, etc.

Always ask yourself: *"Is this truly an IS‑A relationship?"* If the answer is no, choose composition instead. This simple rule will dramatically improve your low-level design skills.
