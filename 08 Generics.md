## Day 8 — Generics Basics

Generics enable types (classes and interfaces) to be parameters when defining classes, interfaces, and methods. They provide **stronger type checks at compile time** and eliminate the need for explicit casting, making code more readable and safer.

### Why Generics?

- **Type safety**: The compiler ensures you only put objects of the correct type into a collection. Without generics, you could accidentally add a `String` to a `List` intended for `Integer`, causing a `ClassCastException` at runtime.
- **Elimination of casts**: You don't need to cast when retrieving elements from a collection.
- **Reusable code**: You can write algorithms that work on different types without duplicating code.

### Common Syntax

- `List<Vehicle>` – a list that can only hold `Vehicle` objects (or its subclasses).
- `Map<String, User>` – a map with keys of type `String` and values of type `User`.
- Generic class: `class Box<T> { ... }`
- Generic method: `public <T> void printArray(T[] array) { ... }`

---

### Problem Statement

Design a simple **Garage Management System** that uses generics to safely store different types of vehicles.

1. Create a **generic class `Garage<T>`** that:
   - Has a `List<T>` to store items of type `T`.
   - Provides methods to `add(T item)`, `remove(T item)`, and `displayAll()`.
   - Has a method `getCount()` that returns the number of items.

2. Create a **hierarchy of vehicles** (as before):
   - `Vehicle` (abstract or concrete) with a `brand` field.
   - `Car` extends `Vehicle`, adds `doors`.
   - `Bike` extends `Vehicle`, adds `gears`.

3. Create a separate class **`User`** with fields `username` and `email`.

4. In a main class, demonstrate:
   - Creating a `Garage<Car>` that only accepts `Car` objects.
   - Creating a `Garage<Bike>` that only accepts `Bike` objects.
   - Attempting to add a `Bike` to the `Garage<Car>` should cause a compile-time error.
   - Creating a `Map<String, User>` to store users by their username, and perform some operations.

5. Also demonstrate a **generic method** that prints the brand of any vehicle (using wildcards or a generic method).

---

### Java Solution

```java
import java.util.*;

// ========== Vehicle hierarchy ==========
class Vehicle {
    protected String brand;
    public Vehicle(String brand) { this.brand = brand; }
    public String getBrand() { return brand; }
    @Override
    public String toString() { return "Vehicle{brand='" + brand + "'}"; }
}

class Car extends Vehicle {
    private int doors;
    public Car(String brand, int doors) { super(brand); this.doors = doors; }
    @Override public String toString() { return "Car{brand='" + brand + "', doors=" + doors + "}"; }
}

class Bike extends Vehicle {
    private int gears;
    public Bike(String brand, int gears) { super(brand); this.gears = gears; }
    @Override public String toString() { return "Bike{brand='" + brand + "', gears=" + gears + "}"; }
}

// ========== Generic Garage class ==========
class Garage<T> {
    private List<T> items = new ArrayList<>();

    public void add(T item) {
        items.add(item);
        System.out.println("Added: " + item);
    }

    public boolean remove(T item) {
        boolean removed = items.remove(item);
        if (removed) System.out.println("Removed: " + item);
        return removed;
    }

    public void displayAll() {
        System.out.println("Garage contents: " + items);
    }

    public int getCount() {
        return items.size();
    }
}

// ========== User class ==========
class User {
    private String username;
    private String email;
    public User(String username, String email) {
        this.username = username;
        this.email = email;
    }
    public String getUsername() { return username; }
    public String getEmail() { return email; }
    @Override public String toString() {
        return "User{username='" + username + "', email='" + email + "'}";
    }
}

// ========== Generic method example ==========
class VehicleUtils {
    // Generic method: prints brand of any Vehicle
    public static <T extends Vehicle> void printBrand(T vehicle) {
        System.out.println("Brand: " + vehicle.getBrand());
    }

    // Wildcard method: processes a list of any Vehicle subclass
    public static void printAllBrands(List<? extends Vehicle> vehicles) {
        for (Vehicle v : vehicles) {
            System.out.println(v.getBrand());
        }
    }
}

// ========== Demo ==========
public class GenericsDemo {
    public static void main(String[] args) {
        // 1. Garage for Cars only
        Garage<Car> carGarage = new Garage<>();
        carGarage.add(new Car("Toyota", 4));
        carGarage.add(new Car("Honda", 4));
        // carGarage.add(new Bike("Giant", 21)); // Compile error: incompatible types

        // 2. Garage for Bikes only
        Garage<Bike> bikeGarage = new Garage<>();
        bikeGarage.add(new Bike("Giant", 21));
        bikeGarage.add(new Bike("Trek", 18));

        System.out.println("\nCar garage count: " + carGarage.getCount());
        carGarage.displayAll();

        System.out.println("\nBike garage count: " + bikeGarage.getCount());
        bikeGarage.displayAll();

        // 3. Map<String, User>
        Map<String, User> userMap = new HashMap<>();
        userMap.put("alice", new User("alice", "alice@example.com"));
        userMap.put("bob", new User("bob", "bob@example.com"));

        System.out.println("\nUser map:");
        for (Map.Entry<String, User> entry : userMap.entrySet()) {
            System.out.println(entry.getKey() + " -> " + entry.getValue());
        }

        // 4. Generic methods
        System.out.println("\nGeneric method printBrand:");
        Car myCar = new Car("Ford", 2);
        VehicleUtils.printBrand(myCar);   // T is inferred as Car

        System.out.println("Wildcard method printAllBrands:");
        List<Vehicle> vehicles = Arrays.asList(new Car("BMW", 4), new Bike("Cannondale", 12));
        VehicleUtils.printAllBrands(vehicles);
    }
}
```

**Output:**

```
Added: Car{brand='Toyota', doors=4}
Added: Car{brand='Honda', doors=4}
Added: Bike{brand='Giant', gears=21}
Added: Bike{brand='Trek', gears=18}

Car garage count: 2
Garage contents: [Car{brand='Toyota', doors=4}, Car{brand='Honda', doors=4}]

Bike garage count: 2
Garage contents: [Bike{brand='Giant', gears=21}, Bike{brand='Trek', gears=18}]

User map:
alice -> User{username='alice', email='alice@example.com'}
bob -> User{username='bob', email='bob@example.com'}

Generic method printBrand:
Brand: Ford
Wildcard method printAllBrands:
BMW
Cannondale
```

---

### Key Takeaways

- **Type safety** with generics: `Garage<Car>` guarantees that only `Car` objects are stored.
- **Compile-time checking** prevents accidental insertion of wrong types.
- **Generic classes** allow creating type‑safe containers without duplicating code.
- **Generic methods** provide flexibility to operate on parameters of various types while preserving type safety.
- **Wildcards** (`? extends Vehicle`) are useful when you want to accept a collection of any subtype.

Generics are fundamental in modern Java, especially when working with collections. Mastering them helps you write cleaner, more robust, and reusable code.
