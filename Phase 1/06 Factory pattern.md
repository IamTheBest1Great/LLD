We’re moving on to the **Factory Pattern**, which is used to encapsulate object creation logic. This pattern is fundamental to the **Open/Closed Principle** and **Dependency Inversion Principle**, allowing client code to depend on abstractions rather than concrete classes.

Over Days 16–20, we’ll cover three common variations:

1. **Simple Factory** – a single class that decides which concrete class to instantiate.
2. **Factory Method** – defines an interface for creating an object, but lets subclasses decide which class to instantiate.
3. **Abstract Factory** – provides an interface for creating families of related or dependent objects.

---

## Day 16 – Simple Factory

### Concept
A **Simple Factory** is not a full design pattern but a programming idiom. It centralises object creation in one method, usually with conditional logic. While it still uses `if-else`/`switch`, it moves that logic out of the main client code.

### Example: Document Creator

#### Folder Structure
```
simple-factory/
└── src/
    └── main/
        └── java/
            └── com/
                └── example/
                    └── document/
                        ├── Document.java
                        ├── PdfDocument.java
                        ├── WordDocument.java
                        ├── DocumentFactory.java
                        └── Main.java
```

#### Code

**Document.java**
```java
package com.example.document;

public interface Document {
    void open();
    void save();
}
```

**PdfDocument.java**
```java
package com.example.document;

public class PdfDocument implements Document {
    @Override
    public void open() { System.out.println("Opening PDF document."); }
    @Override
    public void save() { System.out.println("Saving PDF document."); }
}
```

**WordDocument.java**
```java
package com.example.document;

public class WordDocument implements Document {
    @Override
    public void open() { System.out.println("Opening Word document."); }
    @Override
    public void save() { System.out.println("Saving Word document."); }
}
```

**DocumentFactory.java** – the simple factory
```java
package com.example.document;

public class DocumentFactory {
    public static Document createDocument(String type) {
        if (type.equalsIgnoreCase("pdf")) {
            return new PdfDocument();
        } else if (type.equalsIgnoreCase("word")) {
            return new WordDocument();
        } else {
            throw new IllegalArgumentException("Unknown document type: " + type);
        }
    }
}
```

**Main.java**
```java
package com.example.document;

public class Main {
    public static void main(String[] args) {
        Document doc1 = DocumentFactory.createDocument("pdf");
        doc1.open();

        Document doc2 = DocumentFactory.createDocument("word");
        doc2.save();
    }
}
```

#### Explanation
The factory hides the creation logic. The client only knows the `Document` interface. Adding a new document type (e.g., `ExcelDocument`) requires changing the factory method – a violation of the Open/Closed Principle. That’s why the **Factory Method** pattern is preferred when the system is expected to grow.

---

## Day 17–18 – Factory Method

### Concept
Factory Method defines an interface for creating an object, but lets subclasses decide which class to instantiate. It promotes the Open/Closed Principle: you can add new product types by extending the creator class, without modifying existing code.

### Example: Logistics App (Truck vs Ship)

#### Folder Structure
```
factory-method/
└── src/
    └── main/
        └── java/
            └── com/
                └── example/
                    └── logistics/
                        ├── Transport.java
                        ├── Truck.java
                        ├── Ship.java
                        ├── Logistics.java
                        ├── RoadLogistics.java
                        ├── SeaLogistics.java
                        └── Main.java
```

#### Code

**Transport.java** – product interface
```java
package com.example.logistics;

public interface Transport {
    void deliver();
}
```

**Truck.java**
```java
package com.example.logistics;

public class Truck implements Transport {
    @Override
    public void deliver() {
        System.out.println("Delivering by land in a truck.");
    }
}
```

**Ship.java**
```java
package com.example.logistics;

public class Ship implements Transport {
    @Override
    public void deliver() {
        System.out.println("Delivering by sea in a ship.");
    }
}
```

**Logistics.java** – abstract creator
```java
package com.example.logistics;

public abstract class Logistics {
    // Factory method
    public abstract Transport createTransport();

    // Business logic that uses the factory method
    public void planDelivery() {
        Transport transport = createTransport();
        transport.deliver();
    }
}
```

**RoadLogistics.java** – concrete creator
```java
package com.example.logistics;

public class RoadLogistics extends Logistics {
    @Override
    public Transport createTransport() {
        return new Truck();
    }
}
```

**SeaLogistics.java** – concrete creator
```java
package com.example.logistics;

public class SeaLogistics extends Logistics {
    @Override
    public Transport createTransport() {
        return new Ship();
    }
}
```

**Main.java**
```java
package com.example.logistics;

public class Main {
    public static void main(String[] args) {
        Logistics logistics;

        logistics = new RoadLogistics();
        logistics.planDelivery();  // Delivering by land in a truck.

        logistics = new SeaLogistics();
        logistics.planDelivery();  // Delivering by sea in a ship.
    }
}
```

#### Explanation
- The `Logistics` class declares the factory method `createTransport()`, but does not know which concrete transport will be created.
- Subclasses override the factory method to return the specific transport.
- Adding a new transport (e.g., `Airplane`) means creating a new `AirTransport` and a new `AirLogistics` subclass – no changes to existing code.

---

## Day 19–20 – Abstract Factory

### Concept
Abstract Factory provides an interface for creating families of related objects without specifying their concrete classes. It is often used when a system must be independent of how its products are created, composed, and represented.

### Example: UI Toolkit (Windows vs Mac)

#### Folder Structure
```
abstract-factory/
└── src/
    └── main/
        └── java/
            └── com/
                └── example/
                    └── ui/
                        ├── Button.java
                        ├── Checkbox.java
                        ├── WinButton.java
                        ├── WinCheckbox.java
                        ├── MacButton.java
                        ├── MacCheckbox.java
                        ├── GUIFactory.java
                        ├── WinFactory.java
                        ├── MacFactory.java
                        └── Main.java
```

#### Code

**Button.java** – product A
```java
package com.example.ui;

public interface Button {
    void paint();
}
```

**Checkbox.java** – product B
```java
package com.example.ui;

public interface Checkbox {
    void paint();
}
```

**WinButton.java**
```java
package com.example.ui;

public class WinButton implements Button {
    @Override
    public void paint() { System.out.println("Windows button painted."); }
}
```

**WinCheckbox.java**
```java
package com.example.ui;

public class WinCheckbox implements Checkbox {
    @Override
    public void paint() { System.out.println("Windows checkbox painted."); }
}
```

**MacButton.java**
```java
package com.example.ui;

public class MacButton implements Button {
    @Override
    public void paint() { System.out.println("Mac button painted."); }
}
```

**MacCheckbox.java**
```java
package com.example.ui;

public class MacCheckbox implements Checkbox {
    @Override
    public void paint() { System.out.println("Mac checkbox painted."); }
}
```

**GUIFactory.java** – abstract factory
```java
package com.example.ui;

public interface GUIFactory {
    Button createButton();
    Checkbox createCheckbox();
}
```

**WinFactory.java**
```java
package com.example.ui;

public class WinFactory implements GUIFactory {
    @Override
    public Button createButton() { return new WinButton(); }
    @Override
    public Checkbox createCheckbox() { return new WinCheckbox(); }
}
```

**MacFactory.java**
```java
package com.example.ui;

public class MacFactory implements GUIFactory {
    @Override
    public Button createButton() { return new MacButton(); }
    @Override
    public Checkbox createCheckbox() { return new MacCheckbox(); }
}
```

**Main.java**
```java
package com.example.ui;

public class Main {
    private static Application configureApplication(String os) {
        GUIFactory factory;
        if (os.equalsIgnoreCase("windows")) {
            factory = new WinFactory();
        } else if (os.equalsIgnoreCase("mac")) {
            factory = new MacFactory();
        } else {
            throw new IllegalArgumentException("Unknown OS");
        }
        return new Application(factory);
    }

    public static void main(String[] args) {
        Application app = configureApplication("windows");
        app.paint();  // Windows button painted. / Windows checkbox painted.

        app = configureApplication("mac");
        app.paint();  // Mac button painted. / Mac checkbox painted.
    }
}
```

**Application.java** (client that uses the factory)
```java
package com.example.ui;

public class Application {
    private Button button;
    private Checkbox checkbox;

    public Application(GUIFactory factory) {
        button = factory.createButton();
        checkbox = factory.createCheckbox();
    }

    public void paint() {
        button.paint();
        checkbox.paint();
    }
}
```

#### Explanation
- The `GUIFactory` interface declares methods for creating each product in the family (Button, Checkbox).
- Concrete factories (`WinFactory`, `MacFactory`) produce products that are compatible with a specific operating system.
- The client (`Application`) is written against the abstract factory, so it can work with any family without modification.
- Adding a new family (e.g., Linux) requires implementing new concrete products and a new concrete factory – existing code stays unchanged.

---

## Summary of Factory Variants

| Pattern          | Purpose                                                                 |
|------------------|-------------------------------------------------------------------------|
| Simple Factory   | Centralises creation logic, but violates OCP if new products are added. |
| Factory Method   | Defines an interface for creation, subclasses decide which class to instantiate. Supports OCP. |
| Abstract Factory | Provides an interface for creating families of related objects. Ideal for cross‑platform scenarios. |

### When to Use Factory Patterns
- You have complex object creation logic that should be centralised.
- You want to decouple client code from concrete classes.
- You anticipate adding new product types (Factory Method, Abstract Factory).
- You need to enforce that a family of products is used together (Abstract Factory).

All three patterns are widely used in frameworks and libraries (e.g., JDBC’s `Connection`, `Statement`, etc.) and form the foundation for many other design patterns (e.g., Dependency Injection).
