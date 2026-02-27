## Day 3 — Polymorphism (Runtime Polymorphism)

Polymorphism means "many forms". In Java, **runtime polymorphism** (also called dynamic method dispatch) is achieved through method overriding. When a superclass reference variable refers to a subclass object, the overridden method of the subclass is called at runtime, not the method defined in the superclass.

This allows us to write code that works with objects of multiple types without knowing their exact class at compile time. The decision of which method to call is made **at runtime** based on the actual object type.

### IS-A Relationship Reminder

Just like inheritance, polymorphism relies on the IS-A relationship:
- A **Dog** IS-A **Animal**
- A **Cat** IS-A **Animal**

Therefore, a reference variable of type `Animal` can point to any object that is a subclass of `Animal`.

---

### Problem Statement

Design a simple animal hierarchy to demonstrate runtime polymorphism.

1. Create a base class **Animal** with:
   - A method `speak()` that prints a generic message like `"Animal speaks"`.

2. Create at least two derived classes:
   - **Dog** that overrides `speak()` to print `"Dog barks"`.
   - **Cat** that overrides `speak()` to print `"Cat meows"`.

3. In a main method:
   - Create an array of `Animal` references.
   - Fill it with objects of different subclasses (`Dog`, `Cat`).
   - Loop through the array and call `speak()` on each element.
   - Observe that the correct overridden method is invoked based on the actual object, not the reference type.

4. (Optional) Show how this enables polymorphic behavior, e.g., passing different animals to a method that accepts an `Animal` parameter.

---

### Why This Matters: Foundation of the Strategy Pattern

Runtime polymorphism is the core mechanism behind the **Strategy pattern**. In the Strategy pattern, you define a family of algorithms (like different speaking behaviors) and make them interchangeable. The context (here the `Animal` reference) can use any algorithm without knowing its concrete implementation, because all algorithms implement the same interface or extend the same abstract class. Changing the behavior at runtime is as simple as assigning a different subclass object.

---

### Java Solution

```java
// Base class Animal
class Animal {
    public void speak() {
        System.out.println("Animal speaks");
    }
}

// Derived class Dog
class Dog extends Animal {
    @Override
    public void speak() {
        System.out.println("Dog barks");
    }
}

// Derived class Cat
class Cat extends Animal {
    @Override
    public void speak() {
        System.out.println("Cat meows");
    }
}

// Another derived class for demonstration
class Cow extends Animal {
    @Override
    public void speak() {
        System.out.println("Cow moos");
    }
}

// Main class to demonstrate runtime polymorphism
public class PolymorphismDemo {
    public static void main(String[] args) {
        // Create an array of Animal references
        Animal[] animals = new Animal[4];

        // Populate the array with different animal objects
        animals[0] = new Dog();
        animals[1] = new Cat();
        animals[2] = new Cow();
        animals[3] = new Dog();  // another dog

        // Loop through the array and call speak()
        // The JVM decides at runtime which speak() to call
        System.out.println("Polymorphic behavior:");
        for (Animal a : animals) {
            a.speak();   // calls the overridden method of the actual object
        }

        // Example: method that takes an Animal reference
        System.out.println("\nPassing different animals to a method:");
        makeAnimalSpeak(new Dog());
        makeAnimalSpeak(new Cat());
        makeAnimalSpeak(new Cow());
    }

    // This method accepts any Animal (and its subclasses)
    public static void makeAnimalSpeak(Animal animal) {
        animal.speak();   // runtime polymorphism in action
    }
}
```

**Output:**

```
Polymorphic behavior:
Dog barks
Cat meows
Cow moos
Dog barks

Passing different animals to a method:
Dog barks
Cat meows
Cow moos
```

---

### Explanation

- The `Animal` class defines a generic `speak()` method.
- Each subclass (`Dog`, `Cat`, `Cow`) overrides `speak()` with its own implementation.
- In `main()`, we create an array of type `Animal[]` and store `Dog`, `Cat`, and `Cow` objects in it.
- When we call `a.speak()` inside the loop, Java determines the actual type of the object (e.g., `Dog`) at runtime and invokes the overridden method from that class.
- The same happens in the `makeAnimalSpeak(Animal animal)` method – we can pass any subclass, and the correct `speak()` is called.

This is runtime polymorphism: the method to execute is decided **dynamically** based on the object's type, not the reference's type.

---

### Key Takeaways

- **Runtime polymorphism** is achieved through method overriding.
- A superclass reference can refer to a subclass object.
- The overridden method in the subclass is called, even if the reference is of the superclass type.
- This enables writing flexible and extensible code – new animal types can be added without modifying existing code that uses the `Animal` reference.
- This concept is the basis of many design patterns, especially the **Strategy pattern**, where you can swap behaviors (like different speaking styles) at runtime.

Practice by adding more animal classes and experimenting with different polymorphic scenarios.
