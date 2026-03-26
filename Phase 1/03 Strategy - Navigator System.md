Below is a complete example of a **navigation system** built with the Strategy Pattern. The system supports three route strategies: **Car**, **Bike**, and **Walking**. The pattern allows us to switch between different route calculation algorithms at runtime without modifying the context class.

## Folder Structure

```
navigation-system/
└── src/
    └── main/
        └── java/
            └── com/
                └── example/
                    └── navigation/
                        ├── RouteStrategy.java
                        ├── CarRoute.java
                        ├── BikeRoute.java
                        ├── WalkingRoute.java
                        ├── Navigator.java
                        └── Main.java
```

---

## File Contents

### 1. `RouteStrategy.java` – Strategy Interface

```java
package com.example.navigation;

/**
 * The strategy interface declares the operation common to all
 * supported route calculation algorithms.
 */
public interface RouteStrategy {
    String calculateRoute(String start, String end);
}
```

**Explanation:**  
All route strategies must implement `calculateRoute()`, which takes a start and end location and returns a route description (as a string for simplicity).

---

### 2. `CarRoute.java` – Concrete Strategy for Car

```java
package com.example.navigation;

public class CarRoute implements RouteStrategy {
    @Override
    public String calculateRoute(String start, String end) {
        return "Car route from " + start + " to " + end +
                ": Take highway, avoid tolls, estimated time 30 min.";
    }
}
```

**Explanation:**  
This implementation provides a car‑specific route, using highways and possibly toll information.

---

### 3. `BikeRoute.java` – Concrete Strategy for Bike

```java
package com.example.navigation;

public class BikeRoute implements RouteStrategy {
    @Override
    public String calculateRoute(String start, String end) {
        return "Bike route from " + start + " to " + end +
                ": Use bike lanes, avoid steep hills, estimated time 1 hour.";
    }
}
```

**Explanation:**  
The bike route prefers bike lanes and avoids hills, with a longer estimated time.

---

### 4. `WalkingRoute.java` – Concrete Strategy for Walking

```java
package com.example.navigation;

public class WalkingRoute implements RouteStrategy {
    @Override
    public String calculateRoute(String start, String end) {
        return "Walking route from " + start + " to " + end +
                ": Use sidewalks, cross at lights, estimated time 2 hours.";
    }
}
```

**Explanation:**  
The walking route focuses on pedestrian‑friendly paths and crosswalks.

---

### 5. `Navigator.java` – Context

```java
package com.example.navigation;

/**
 * The context holds a reference to a RouteStrategy and delegates
 * the route calculation to it.
 */
public class Navigator {
    private RouteStrategy strategy;

    public void setRouteStrategy(RouteStrategy strategy) {
        this.strategy = strategy;
    }

    public String getRoute(String start, String end) {
        if (strategy == null) {
            return "No route strategy selected.";
        }
        return strategy.calculateRoute(start, end);
    }
}
```

**Explanation:**  
The `Navigator` class is the context. It maintains a reference to a `RouteStrategy` (which can be set at runtime). The `getRoute()` method delegates the calculation to the current strategy. The context is unaware of the concrete strategy used.

---

### 6. `Main.java` – Client Code

```java
package com.example.navigation;

public class Main {
    public static void main(String[] args) {
        Navigator navigator = new Navigator();

        // Use car route
        navigator.setRouteStrategy(new CarRoute());
        System.out.println(navigator.getRoute("Home", "Office"));

        // Switch to bike route
        navigator.setRouteStrategy(new BikeRoute());
        System.out.println(navigator.getRoute("Home", "Office"));

        // Switch to walking route
        navigator.setRouteStrategy(new WalkingRoute());
        System.out.println(navigator.getRoute("Home", "Office"));
    }
}
```

**Output:**

```
Car route from Home to Office: Take highway, avoid tolls, estimated time 30 min.
Bike route from Home to Office: Use bike lanes, avoid steep hills, estimated time 1 hour.
Walking route from Home to Office: Use sidewalks, cross at lights, estimated time 2 hours.
```

---

## How the Pattern Works Together

1. **Strategy Interface (`RouteStrategy`)** – declares a common method `calculateRoute()` that all route algorithms must implement.
2. **Concrete Strategies (`CarRoute`, `BikeRoute`, `WalkingRoute`)** – each implements the route calculation with its own logic (different route types, estimated times, etc.).
3. **Context (`Navigator`)** – holds a reference to a `RouteStrategy` and delegates the route calculation to it. The context never knows which concrete strategy it is using.
4. **Client (`Main`)** – decides which strategy to use and sets it on the context. This decision can be based on user input, preferences, or other runtime conditions.

## Benefits of This Structure

- **Open/Closed Principle** – adding a new route type (e.g., `PublicTransportRoute`) requires only a new class implementing `RouteStrategy`; existing code remains unchanged.
- **Single Responsibility** – each strategy encapsulates one specific route calculation; the `Navigator` focuses only on managing the strategy and delegating.
- **Runtime Flexibility** – strategies can be swapped on the fly (as shown in the example).
- **Testability** – each route algorithm can be unit‑tested independently.

This example demonstrates the Strategy Pattern in a real‑world scenario, allowing a navigation system to support multiple route types cleanly and maintainably.
