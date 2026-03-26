Let's implement a **Simple Factory** for shapes – specifically **Circle** and **Square**. The factory centralizes the creation logic, allowing clients to request shapes without knowing the concrete classes.

## Folder Structure

```
simple-shape-factory/
└── src/
    └── main/
        └── java/
            └── com/
                └── example/
                    └── shape/
                        ├── Shape.java
                        ├── Circle.java
                        ├── Square.java
                        ├── ShapeFactory.java
                        └── Main.java
```

---

## Code

### 1. `Shape.java` – Product Interface

```java
package com.example.shape;

public interface Shape {
    void draw();
}
```

### 2. `Circle.java` – Concrete Product

```java
package com.example.shape;

public class Circle implements Shape {
    @Override
    public void draw() {
        System.out.println("Drawing a Circle");
    }
}
```

### 3. `Square.java` – Concrete Product

```java
package com.example.shape;

public class Square implements Shape {
    @Override
    public void draw() {
        System.out.println("Drawing a Square");
    }
}
```

### 4. `ShapeFactory.java` – Simple Factory

```java
package com.example.shape;

public class ShapeFactory {
    public static Shape createShape(String type) {
        if (type == null) {
            throw new IllegalArgumentException("Shape type cannot be null");
        }
        if (type.equalsIgnoreCase("circle")) {
            return new Circle();
        } else if (type.equalsIgnoreCase("square")) {
            return new Square();
        } else {
            throw new IllegalArgumentException("Unknown shape type: " + type);
        }
    }
}
```

### 5. `Main.java` – Client

```java
package com.example.shape;

public class Main {
    public static void main(String[] args) {
        Shape shape1 = ShapeFactory.createShape("circle");
        shape1.draw();

        Shape shape2 = ShapeFactory.createShape("square");
        shape2.draw();
    }
}
```

**Output:**

```
Drawing a Circle
Drawing a Square
```

---

## How It Works

- The **`Shape`** interface defines the common behavior (draw).
- **`Circle`** and **`Square`** are concrete implementations.
- **`ShapeFactory`** contains a static method `createShape(String type)` that decides which concrete shape to instantiate based on the input string.
- **`Main`** (client) uses the factory to obtain shape objects, without ever referencing `Circle` or `Square` directly.

## Benefits & Drawbacks

**Benefits:**
- Centralizes object creation logic.
- Reduces duplication (client code does not need to repeat `new Circle()` or `new Square()`).
- Makes it easier to change the instantiation process (e.g., add logging, caching, or complex configuration) without affecting clients.

**Drawbacks:**
- The factory method grows each time a new shape is added (violates the Open/Closed Principle). This is why Simple Factory is often considered a stepping stone to the **Factory Method** pattern.

This example gives you a clean starting point for understanding the factory concept.
