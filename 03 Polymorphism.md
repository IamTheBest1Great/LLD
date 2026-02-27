## Day 3: Polymorphism (VERY IMPORTANT) – Detailed Notes

Polymorphism is one of the four fundamental OOP concepts (along with encapsulation, inheritance, and abstraction). It allows objects of different classes to be treated as objects of a common superclass, and the correct method implementation is determined at runtime. This is the cornerstone of many design patterns, especially the **Strategy pattern**.

---

### 1. What is Polymorphism?

- **Polymorphism** means "many forms." In Java, it refers to the ability of a single method or class to take on multiple forms.
- There are two types:
  - **Compile-time polymorphism (method overloading)** – resolved during compilation.
  - **Runtime polymorphism (method overriding)** – resolved during execution (the focus of today).

---

### 2. Compile-time vs Runtime Polymorphism

| Type                | Mechanism            | Resolution Time | Example                 |
|---------------------|----------------------|-----------------|-------------------------|
| Compile-time        | Method overloading   | At compilation  | Multiple methods with same name but different parameters |
| Runtime (Dynamic)   | Method overriding    | At runtime      | Subclass provides specific implementation of a superclass method |

Today we dive deep into **runtime polymorphism**.

---

### 3. Runtime Polymorphism (Method Overriding)

- Occurs when a subclass provides a specific implementation of a method that is already defined in its superclass.
- The method to call is decided at runtime based on the **actual object type**, not the reference type.
- This is achieved through **dynamic method dispatch**.

**Key Requirements:**
- Inheritance (IS-A relationship)
- Method overriding (same signature, same or covariant return type, not `private`/`final`/`static`)
- The superclass reference points to a subclass object

---

### 4. Classic Example: Animal Hierarchy

Let's model animals with a `speak()` method that each animal overrides.

#### Folder Structure:
```
Day03-Polymorphism/
├── src/
│   └── com/
│       └── example/
│           └── animal/
│               ├── Animal.java
│               ├── Dog.java
│               ├── Cat.java
│               └── Main.java
└── README.md (optional)
```

#### Animal.java (Superclass)
```java
package com.example.animal;

public class Animal {
    private String name;

    public Animal(String name) {
        this.name = name;
    }

    public String getName() {
        return name;
    }

    // Method to be overridden
    public void speak() {
        System.out.println("Animal speaks");
    }
}
```

#### Dog.java (Subclass)
```java
package com.example.animal;

public class Dog extends Animal {
    public Dog(String name) {
        super(name);
    }

    @Override
    public void speak() {
        System.out.println(getName() + " says: Woof!");
    }

    // Dog-specific method
    public void fetch() {
        System.out.println(getName() + " is fetching the ball.");
    }
}
```

#### Cat.java (Subclass)
```java
package com.example.animal;

public class Cat extends Animal {
    public Cat(String name) {
        super(name);
    }

    @Override
    public void speak() {
        System.out.println(getName() + " says: Meow!");
    }
}
```

---

### 5. Dynamic Method Dispatch in Action

Now let's see how runtime polymorphism works.

#### Main.java
```java
package com.example.animal;

public class Main {
    public static void main(String[] args) {
        // Creating objects
        Animal myAnimal = new Animal("Generic");
        Animal myDog = new Dog("Buddy");   // Upcasting
        Animal myCat = new Cat("Whiskers"); // Upcasting

        // Calling overridden methods
        myAnimal.speak(); // Animal speaks
        myDog.speak();    // Buddy says: Woof!
        myCat.speak();    // Whiskers says: Meow!

        // Demonstrating array of animals
        Animal[] animals = { new Dog("Max"), new Cat("Luna"), new Animal("Unknown") };
        for (Animal a : animals) {
            a.speak(); // Correct version called for each
        }

        // Dog-specific method – need downcasting
        // myDog.fetch(); // Compilation error – Animal reference doesn't know fetch()
        if (myDog instanceof Dog) {
            ((Dog) myDog).fetch(); // Downcasting to Dog
        }
    }
}
```

**Output:**
```
Animal speaks
Buddy says: Woof!
Whiskers says: Meow!
Max says: Woof!
Luna says: Meow!
Animal speaks
Buddy is fetching the ball.
```

**Explanation:**
- Even though `myDog` is declared as `Animal`, it actually holds a `Dog` object. At runtime, Java uses the **actual object type** to determine which `speak()` to call. This is **dynamic method dispatch**.
- This allows us to write code that works with the superclass type but executes subclass-specific behavior.

---

### 6. Upcasting and Downcasting

- **Upcasting:** Assigning a subclass object to a superclass reference (done automatically). It's safe because a subclass IS-A superclass.  
  `Animal a = new Dog();`
- **Downcasting:** Converting a superclass reference back to a subclass type. Must be done explicitly and checked with `instanceof` to avoid `ClassCastException`.  
  `if (a instanceof Dog) { Dog d = (Dog) a; }`

---

### 7. Polymorphism with Interfaces

Polymorphism also works with interfaces. A class implementing an interface can be referenced by the interface type. This is extremely common in design patterns.

**Example:**
```java
interface Speakable {
    void speak();
}

class Robot implements Speakable {
    @Override
    public void speak() {
        System.out.println("Robot says: Beep boop");
    }
}

// Usage
Speakable s = new Robot();
s.speak();
```

This is actually the basis of the **Strategy pattern** – we define a family of algorithms (behaviors) as interfaces, and concrete classes implement them.

---

### 8. Why Polymorphism is the Foundation of the Strategy Pattern

The **Strategy pattern** defines a family of algorithms, encapsulates each one, and makes them interchangeable. It lets the algorithm vary independently from clients that use it.

- In Strategy, we have a **context** that holds a reference to a **strategy interface**.
- The actual strategy object (concrete implementation) is injected at runtime.
- The context calls the strategy's method, and the correct implementation is invoked **polymorphically**.

**Analogy with Animal example:**
- `Animal` is like the strategy interface (or abstract class).
- `Dog`, `Cat` are concrete strategies.
- The client (e.g., `Main`) treats them uniformly as `Animal` and calls `speak()`, but the actual behavior depends on the object type.

Thus, mastering runtime polymorphism is crucial for implementing many behavioral design patterns.

---

### 9. Practice Exercise

1. Add a new subclass `Duck` that extends `Animal` and overrides `speak()` to print "Quack!".
2. Create a method `performSpeak(Animal animal)` in `Main` that simply calls `animal.speak()`. Pass different animals to it.
3. Create an interface `Pet` with a method `play()`. Make `Dog` and `Cat` implement `Pet` (in addition to extending `Animal`). Demonstrate polymorphism with interface references.

---

### 10. Key Takeaways

- **Runtime polymorphism** enables dynamic method dispatch based on actual object type.
- It requires inheritance and method overriding.
- Upcasting allows treating objects generically; downcasting retrieves specific behavior.
- Interfaces also support polymorphism and are preferred for loose coupling.
- Polymorphism is the engine behind many design patterns (Strategy, Observer, Command, etc.).
- Always program to an interface/supertype, not the implementation – this is a key design principle.

---

### Summary

Today we explored the power of polymorphism. You saw how a single method call (`speak()`) can produce different outputs depending on the object's actual class, even when using a superclass reference. This is the magic of OOP that enables flexible and extensible designs.

Next, we'll build on this foundation to implement the **Strategy pattern** (which is essentially polymorphism applied to algorithms). Keep practicing with different hierarchies and interfaces!
