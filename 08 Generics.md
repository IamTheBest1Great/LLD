## Day 8 — Generics Basics (Understand: `List<Vehicle>`, `Map<String, User>`)

Generics are a powerful feature in Java that enable types (classes and interfaces) to be parameters when defining classes, interfaces, and methods. They provide stronger type checks at compile time and eliminate the need for explicit casting. In Low-Level Design, generics are essential for creating reusable and type-safe data structures and algorithms.

---

### 1. What Are Generics?

- **Generics** allow you to abstract over types.
- They enable you to write code that can work with different data types while providing compile-time type safety.
- The most familiar example is collection classes like `List<T>`, where `T` is a type parameter.

**Without generics (pre-Java 5):**
```java
List list = new ArrayList();
list.add("hello");
list.add(123); // allowed, but unsafe
String s = (String) list.get(0); // need explicit cast
String s2 = (String) list.get(1); // ClassCastException at runtime
```

**With generics:**
```java
List<String> list = new ArrayList<>();
list.add("hello");
// list.add(123); // compile error: type mismatch
String s = list.get(0); // no cast needed
```

---

### 2. Why Use Generics?

- **Type safety**: Catch type errors at compile time.
- **Eliminate casts**: No need to cast when retrieving elements.
- **Code reuse**: Write a single class/method that works with many types.
- **Better readability**: Intent is clear (e.g., `List<String>`).

---

### 3. Generic Classes

You can define your own generic classes. Syntax: `class ClassName<T> { ... }`

**Example: A generic `Box` class**
```java
public class Box<T> {
    private T content;

    public void set(T content) {
        this.content = content;
    }

    public T get() {
        return content;
    }
}
```

**Usage:**
```java
Box<String> stringBox = new Box<>();
stringBox.set("Hello");
String value = stringBox.get(); // no cast

Box<Integer> intBox = new Box<>();
intBox.set(123);
Integer intValue = intBox.get();
```

---

### 4. Generic Interfaces

Interfaces can also be generic.

**Example: A generic `Pair` interface**
```java
public interface Pair<K, V> {
    K getKey();
    V getValue();
}
```

**Implementation:**
```java
public class OrderedPair<K, V> implements Pair<K, V> {
    private K key;
    private V value;

    public OrderedPair(K key, V value) {
        this.key = key;
        this.value = value;
    }

    @Override
    public K getKey() { return key; }

    @Override
    public V getValue() { return value; }
}
```

**Usage:**
```java
Pair<String, Integer> pair = new OrderedPair<>("Age", 30);
```

---

### 5. Generic Methods

You can also write generic methods inside non-generic classes. The type parameter is declared before the return type.

**Example: Generic method to convert an array to a list**
```java
public class Utils {
    public static <T> List<T> arrayToList(T[] array) {
        List<T> list = new ArrayList<>();
        for (T element : array) {
            list.add(element);
        }
        return list;
    }
}
```

**Usage:**
```java
String[] strArray = {"a", "b", "c"};
List<String> strList = Utils.arrayToList(strArray);

Integer[] intArray = {1, 2, 3};
List<Integer> intList = Utils.arrayToList(intArray);
```

---

### 6. Type Parameter Naming Conventions

By convention, type parameter names are single uppercase letters:

- `E` – Element (used extensively by Java Collections)
- `K` – Key
- `V` – Value
- `N` – Number
- `T` – Type
- `S, U, V` etc. – second, third, fourth types

---

### 7. Bounded Type Parameters

Sometimes you want to restrict the types that can be used as type arguments. Use `extends` to specify an upper bound.

**Example: A method that works only with subclasses of `Number`**
```java
public static <T extends Number> double sum(T a, T b) {
    return a.doubleValue() + b.doubleValue();
}
```

**Usage:**
```java
sum(10, 20);      // Integer extends Number – OK
sum(10.5, 20.7);  // Double extends Number – OK
// sum("a", "b"); // compile error: String does not extend Number
```

You can also have multiple bounds: `<T extends A & B & C>` (A must be a class or interface, B and C must be interfaces).

---

### 8. Wildcards (`?`)

Wildcards represent an unknown type. They are used in method signatures and field declarations where you need to express that the exact type is not important, or you need to provide flexibility.

- **Unbounded wildcard**: `?` – stands for any type.
- **Upper bounded wildcard**: `? extends Type` – any type that is a subtype of `Type`.
- **Lower bounded wildcard**: `? super Type` – any type that is a supertype of `Type`.

#### Unbounded Wildcard
```java
public static void printList(List<?> list) {
    for (Object elem : list) {
        System.out.println(elem);
    }
}
```
You can read from `list` as `Object`, but you cannot add elements (except `null`) because the type is unknown.

#### Upper Bounded Wildcard (`? extends`)
```java
public static double sumOfList(List<? extends Number> list) {
    double sum = 0.0;
    for (Number num : list) {
        sum += num.doubleValue();
    }
    return sum;
}
```
You can read from `list` as `Number`, but you cannot add (because you don't know the specific subtype).

#### Lower Bounded Wildcard (`? super`)
```java
public static void addNumbers(List<? super Integer> list) {
    for (int i = 1; i <= 5; i++) {
        list.add(i); // you can add Integer
    }
}
```
You can add `Integer` (or its subtypes) to a list that is a supertype of `Integer` (like `List<Number>` or `List<Object>`).

**PECS (Producer Extends, Consumer Super)**:  
- Use `? extends T` when you *produce* (read) items from a structure.  
- Use `? super T` when you *consume* (write) items into a structure.

---

### 9. Type Erasure

Java generics are implemented using **type erasure**. This means that generic type information is only present at compile time and is removed (erased) during compilation. The compiler replaces type parameters with their bounds or `Object` and adds necessary casts. This ensures backward compatibility with pre-generics code.

**Consequences:**
- You cannot use primitive types as type parameters (use wrapper classes).
- You cannot create instances of type parameters (e.g., `new T()`).
- You cannot have static fields of type parameters.
- You cannot use `instanceof` with parameterized types (except unbounded wildcards).
- You cannot create arrays of parameterized types (e.g., `new List<String>[10]`) – though you can use `ArrayList` or `List<List<String>>`.

---

### 10. Examples Relevant to LLD

#### `List<Vehicle>`
```java
List<Vehicle> vehicles = new ArrayList<>();
vehicles.add(new Car());
vehicles.add(new Bike()));
Vehicle v = vehicles.get(0); // no cast needed
```

#### `Map<String, User>`
```java
Map<String, User> userMap = new HashMap<>();
userMap.put("john123", new User("John"));
User user = userMap.get("john123");
```

#### Generic Repository Pattern
```java
public interface Repository<T, ID> {
    T findById(ID id);
    void save(T entity);
    void delete(ID id);
}

public class UserRepository implements Repository<User, Long> {
    private Map<Long, User> store = new HashMap<>();

    @Override
    public User findById(Long id) {
        return store.get(id);
    }

    @Override
    public void save(User user) {
        store.put(user.getId(), user);
    }

    @Override
    public void delete(Long id) {
        store.remove(id);
    }
}
```

---

### 11. Practice Exercise

1. **Create a generic `Cache` class** that stores key-value pairs with a maximum size. Use `Map<K, V>` internally. Add methods `put(K key, V value)`, `get(K key)`, and `evict()` (remove eldest if size exceeds limit). (Hint: Use `LinkedHashMap` with `removeEldestEntry`.)

2. **Write a generic method `swap`** that swaps two elements in an array at given indices.

3. **Design a generic `Result` class** that can hold either a success value of type `T` or an error message (`String`). (Similar to `Optional` but with error.)

4. **Modify the ParkingLot from Day 7** to use generics:  
   - Create a generic `ParkingSpot<T extends Vehicle>` where `T` is the vehicle type.  
   - Use `Map<VehicleType, Queue<ParkingSpot<?>>>` for available spots.

---

### 12. Key Takeaways

- Generics provide compile-time type safety and eliminate casting.
- Use `<>` (diamond operator) to infer type arguments.
- Bounded type parameters (`<T extends SomeClass>`) restrict what types can be used.
- Wildcards (`?`, `? extends`, `? super`) add flexibility when you don't need exact type.
- Remember PECS for wildcard usage.
- Type erasure means generic type info is not available at runtime.

Generics are fundamental for writing reusable, type-safe code. They are used extensively in collections, and you'll encounter them frequently in LLD (e.g., repository patterns, caches, strategy containers). Practice by converting non-generic code to generic. Tomorrow we'll apply these concepts in the **Strategy Pattern** implementation.
