## Day 7 — Collections for LLD (Java Collections Framework)

Today we'll dive into the **Java Collections Framework** — a set of interfaces and classes that provide ready-to-use data structures. In Low-Level Design, you'll constantly use collections to manage data like lists of vehicles, maps of tickets, queues of waiting customers, sets of unique IDs, etc. Choosing the right collection can make your code efficient, readable, and maintainable.

---

### 1. What is the Java Collections Framework?

The Collections Framework is a unified architecture for storing and manipulating groups of objects. It includes:

- **Interfaces**: Define the core collection types (e.g., `List`, `Set`, `Queue`, `Map`).
- **Implementations**: Concrete classes that implement the interfaces (e.g., `ArrayList`, `HashSet`, `HashMap`).
- **Algorithms**: Static methods in `Collections` class for sorting, searching, etc.

All collections are in the `java.util` package.

---

### 2. Core Interfaces

#### Collection Interface (root of the collection hierarchy)
- `List`, `Set`, and `Queue` extend `Collection`.
- Provides basic methods like `add()`, `remove()`, `size()`, `iterator()`.

#### List Interface
- Ordered collection (sequence).
- Allows duplicate elements.
- Access by index.

#### Set Interface
- Unordered collection (no duplicates).
- Models mathematical set.

#### Queue Interface
- Collection designed for holding elements prior to processing.
- Typically FIFO (but not required; e.g., `PriorityQueue` orders by priority).

#### Map Interface
- Not part of `Collection` interface, but part of the framework.
- Stores key-value pairs.
- Each key maps to at most one value.

---

### 3. List Implementations

#### ArrayList
- Resizable array implementation.
- **Fast random access** (get/set by index: O(1)).
- **Adding/removing at the end** is amortized O(1).
- **Inserting/removing in the middle** is O(n) because elements must shift.
- **Use when**: you need fast iteration, random access, and primarily add/remove at the end.

```java
List<String> parkingTickets = new ArrayList<>();
parkingTickets.add("T123");
parkingTickets.add("T456");
String firstTicket = parkingTickets.get(0); // "T123"
```

#### LinkedList
- Doubly-linked list implementation.
- **Random access** is O(n) (must traverse from head).
- **Adding/removing at beginning or end** is O(1).
- Can be used as a queue or deque.
- **Use when**: you frequently add/remove at both ends, or need a queue/stack.

```java
Queue<String> waitingQueue = new LinkedList<>();
waitingQueue.offer("Car1");
waitingQueue.offer("Car2");
String next = waitingQueue.poll(); // "Car1"
```

---

### 4. Set Implementations

#### HashSet
- Hash table implementation.
- **No order guarantees**.
- **add, remove, contains**: O(1) average.
- Allows one `null`.
- **Use when**: you need a fast set with no duplicates and don't care about order.

```java
Set<String> registeredVehicles = new HashSet<>();
registeredVehicles.add("ABC123");
registeredVehicles.add("XYZ789");
boolean exists = registeredVehicles.contains("ABC123"); // true
```

#### LinkedHashSet
- Hash table + linked list (maintains insertion order).
- Slightly slower than `HashSet`, but predictable iteration order.
- **Use when**: you need a set with no duplicates but want to preserve insertion order.

```java
Set<String> entryOrder = new LinkedHashSet<>();
entryOrder.add("Car1");
entryOrder.add("Bike1");
// Iteration returns "Car1", "Bike1"
```

#### TreeSet
- Red-black tree implementation (sorted).
- Elements are sorted either by natural order or a custom `Comparator`.
- **add, remove, contains**: O(log n).
- Does not allow `null` (if using natural ordering, because `null` cannot be compared).
- **Use when**: you need a sorted set (e.g., available parking spots by spot number).

```java
Set<Integer> availableSpots = new TreeSet<>();
availableSpots.add(5);
availableSpots.add(2);
availableSpots.add(7);
// Iteration returns 2,5,7
```

---

### 5. Queue Implementations

#### PriorityQueue
- Implements a priority heap.
- Elements are ordered according to natural order or a `Comparator`.
- **offer, poll, remove**: O(log n).
- **peek**: O(1).
- **Use when**: you need to process elements based on priority (e.g., emergency vehicles first, nearest parking spot).

```java
Queue<ParkingSpot> priorityQueue = new PriorityQueue<>(
    (s1, s2) -> Integer.compare(s1.getDistance(), s2.getDistance())
);
priorityQueue.offer(new ParkingSpot(1, 10)); // distance 10
priorityQueue.offer(new ParkingSpot(2, 5));  // distance 5
ParkingSpot nearest = priorityQueue.poll();  // spot with distance 5
```

#### ArrayDeque
- Resizable array implementation of `Deque` (double-ended queue).
- Faster than `LinkedList` for stack/queue operations.
- **addFirst, addLast, removeFirst, removeLast**: O(1) amortized.
- Can be used as a stack (LIFO) or queue (FIFO).
- **Use when**: you need a queue/stack with efficient operations at both ends.

```java
Deque<String> stack = new ArrayDeque<>();
stack.push("A"); // addFirst
stack.push("B");
String top = stack.pop(); // "B"
```

---

### 6. Map Implementations

#### HashMap
- Hash table implementation.
- **get, put, remove**: O(1) average.
- Allows one `null` key and multiple `null` values.
- No order guarantees.
- **Use when**: you need fast key-value lookups, e.g., mapping ticket ID to ticket object.

```java
Map<String, ParkingTicket> ticketMap = new HashMap<>();
ticketMap.put("T123", new ParkingTicket("T123", VehicleType.CAR));
ParkingTicket ticket = ticketMap.get("T123");
```

#### LinkedHashMap
- Hash table + linked list (maintains insertion order or access order).
- Slightly slower than `HashMap`.
- **Use when**: you need predictable iteration order (e.g., display tickets in order of issue).
- Can be used to build an LRU cache by setting `accessOrder=true` and overriding `removeEldestEntry`.

```java
Map<String, Integer> lruCache = new LinkedHashMap<>(16, 0.75f, true) {
    @Override
    protected boolean removeEldestEntry(Map.Entry<String, Integer> eldest) {
        return size() > 100; // keep only 100 entries
    }
};
```

#### TreeMap
- Red-black tree implementation (sorted by keys).
- **put, get, remove**: O(log n).
- Keys must be `Comparable` or a `Comparator` provided.
- **Use when**: you need a sorted map, e.g., parking spots sorted by spot number.

```java
Map<Integer, ParkingSpot> spotMap = new TreeMap<>();
spotMap.put(10, new ParkingSpot(10));
spotMap.put(5, new ParkingSpot(5));
// firstKey() returns 5
```

#### EnumMap
- Specialized `Map` implementation for enum keys.
- Extremely efficient (uses array internally).
- Keys must be of a single enum type.
- **Use when**: you have enum keys (e.g., `VehicleType` mapped to available spots).

```java
EnumMap<VehicleType, Integer> spotCounts = new EnumMap<>(VehicleType.class);
spotCounts.put(VehicleType.CAR, 50);
spotCounts.put(VehicleType.BIKE, 20);
int carSpots = spotCounts.get(VehicleType.CAR); // 50
```

---

### 7. Choosing the Right Collection – Quick Reference

| Need                                   | List                      | Set                       | Queue                     | Map                       |
|----------------------------------------|---------------------------|---------------------------|---------------------------|---------------------------|
| Ordered, duplicates allowed            | `ArrayList` / `LinkedList`| —                         | —                         | —                         |
| No duplicates, no order                | —                         | `HashSet`                 | —                         | —                         |
| No duplicates, insertion order         | —                         | `LinkedHashSet`           | —                         | —                         |
| Sorted unique elements                 | —                         | `TreeSet`                 | —                         | —                         |
| FIFO queue                             | —                         | —                         | `LinkedList` / `ArrayDeque` | —                         |
| Priority queue                         | —                         | —                         | `PriorityQueue`           | —                         |
| Key-value pairs, no order              | —                         | —                         | —                         | `HashMap`                 |
| Key-value pairs, insertion order       | —                         | —                         | —                         | `LinkedHashMap`           |
| Key-value pairs, sorted by key         | —                         | —                         | —                         | `TreeMap`                 |
| Enum keys                               | —                         | —                         | —                         | `EnumMap`                 |

---

### 8. Thread-Safe Collections

In multi-threaded environments (common in LLD problems like parking lot with multiple entry/exit gates), you need thread-safe collections.

- **Synchronized wrappers**: `Collections.synchronizedList(new ArrayList<>())` etc. But they synchronize on the collection object, which can be inefficient.
- **`java.util.concurrent` package**:
  - `ConcurrentHashMap` – thread-safe map with high concurrency.
  - `CopyOnWriteArrayList` – thread-safe list; suitable for read-heavy scenarios.
  - `BlockingQueue` implementations (`ArrayBlockingQueue`, `LinkedBlockingQueue`) – thread-safe queues with blocking operations.
  - `ConcurrentLinkedQueue` – non-blocking thread-safe queue.

**Example:**
```java
Map<String, ParkingTicket> concurrentTicketMap = new ConcurrentHashMap<>();
Queue<String> waitingCars = new ConcurrentLinkedQueue<>();
```

---

### 9. Practical Example: Parking Lot System with Collections

Let's design a simple parking lot that uses various collections.

**Requirements:**
- Different vehicle types (CAR, BIKE, TRUCK).
- Each vehicle type has a fixed number of spots.
- When a vehicle arrives, assign the nearest available spot (spots have numbers).
- Keep track of active tickets.
- Support multiple entry/exit gates (thread-safe).

#### Folder Structure
```
Day07-Collections/
├── src/
│   └── com/
│       └── example/
│           └── parking/
│               ├── VehicleType.java (enum)
│               ├── ParkingSpot.java
│               ├── ParkingTicket.java
│               ├── ParkingLot.java
│               └── Main.java
└── README.md
```

#### VehicleType.java (enum)
```java
package com.example.parking;

public enum VehicleType {
    CAR, BIKE, TRUCK
}
```

#### ParkingSpot.java
```java
package com.example.parking;

public class ParkingSpot implements Comparable<ParkingSpot> {
    private final int spotNumber;
    private final VehicleType vehicleType;
    private boolean isAvailable;

    public ParkingSpot(int spotNumber, VehicleType vehicleType) {
        this.spotNumber = spotNumber;
        this.vehicleType = vehicleType;
        this.isAvailable = true;
    }

    public int getSpotNumber() { return spotNumber; }
    public VehicleType getVehicleType() { return vehicleType; }
    public boolean isAvailable() { return isAvailable; }
    public void setAvailable(boolean available) { isAvailable = available; }

    @Override
    public int compareTo(ParkingSpot other) {
        return Integer.compare(this.spotNumber, other.spotNumber);
    }
}
```

#### ParkingTicket.java
```java
package com.example.parking;

import java.time.Instant;

public class ParkingTicket {
    private final String ticketId;
    private final String vehicleNumber;
    private final VehicleType vehicleType;
    private final int spotNumber;
    private final Instant entryTime;
    private Instant exitTime;
    private double fee;

    public ParkingTicket(String ticketId, String vehicleNumber, VehicleType vehicleType, int spotNumber) {
        this.ticketId = ticketId;
        this.vehicleNumber = vehicleNumber;
        this.vehicleType = vehicleType;
        this.spotNumber = spotNumber;
        this.entryTime = Instant.now();
    }

    // getters and setters
    public String getTicketId() { return ticketId; }
    public String getVehicleNumber() { return vehicleNumber; }
    public VehicleType getVehicleType() { return vehicleType; }
    public int getSpotNumber() { return spotNumber; }
    public Instant getEntryTime() { return entryTime; }
    public Instant getExitTime() { return exitTime; }
    public void setExitTime(Instant exitTime) { this.exitTime = exitTime; }
    public double getFee() { return fee; }
    public void setFee(double fee) { this.fee = fee; }
}
```

#### ParkingLot.java (core logic)
```java
package com.example.parking;

import java.util.*;
import java.util.concurrent.ConcurrentHashMap;
import java.util.concurrent.ConcurrentLinkedQueue;
import java.util.concurrent.atomic.AtomicInteger;

public class ParkingLot {
    private final Map<VehicleType, Queue<ParkingSpot>> availableSpots;
    private final Map<VehicleType, Set<ParkingSpot>> allSpots; // for reporting
    private final Map<String, ParkingTicket> activeTickets; // ticketId -> ticket
    private final Map<String, ParkingTicket> vehicleToTicket; // vehicleNumber -> ticket
    private final AtomicInteger ticketCounter = new AtomicInteger(1);

    public ParkingLot() {
        availableSpots = new ConcurrentHashMap<>();
        allSpots = new ConcurrentHashMap<>();
        activeTickets = new ConcurrentHashMap<>();
        vehicleToTicket = new ConcurrentHashMap<>();

        // Initialize spots for each vehicle type
        for (VehicleType type : VehicleType.values()) {
            // Use TreeSet to keep spots sorted, then wrap in ConcurrentLinkedQueue
            Set<ParkingSpot> spots = new TreeSet<>();
            for (int i = 1; i <= 10; i++) {
                ParkingSpot spot = new ParkingSpot(i * 100 + type.ordinal(), type);
                spots.add(spot);
            }
            allSpots.put(type, Collections.synchronizedSet(spots));
            // Initialize availableSpots queue with all spots
            Queue<ParkingSpot> queue = new ConcurrentLinkedQueue<>(spots);
            availableSpots.put(type, queue);
        }
    }

    public ParkingTicket parkVehicle(String vehicleNumber, VehicleType type) {
        // Check if vehicle already parked
        if (vehicleToTicket.containsKey(vehicleNumber)) {
            throw new IllegalStateException("Vehicle already parked");
        }

        Queue<ParkingSpot> queue = availableSpots.get(type);
        ParkingSpot spot = queue.poll();
        if (spot == null) {
            throw new IllegalStateException("No available spot for " + type);
        }

        spot.setAvailable(false);
        String ticketId = "TICKET-" + ticketCounter.getAndIncrement();
        ParkingTicket ticket = new ParkingTicket(ticketId, vehicleNumber, type, spot.getSpotNumber());
        activeTickets.put(ticketId, ticket);
        vehicleToTicket.put(vehicleNumber, ticket);
        return ticket;
    }

    public double exitVehicle(String ticketId) {
        ParkingTicket ticket = activeTickets.remove(ticketId);
        if (ticket == null) {
            throw new IllegalArgumentException("Invalid ticket ID");
        }
        vehicleToTicket.remove(ticket.getVehicleNumber());

        // Calculate fee (simplified: $5 per hour)
        long hours = java.time.Duration.between(ticket.getEntryTime(), java.time.Instant.now()).toHours();
        if (hours == 0) hours = 1;
        double fee = hours * 5.0;
        ticket.setFee(fee);
        ticket.setExitTime(java.time.Instant.now());

        // Free up the spot
        ParkingSpot spot = allSpots.get(ticket.getVehicleType()).stream()
                .filter(s -> s.getSpotNumber() == ticket.getSpotNumber())
                .findFirst().orElseThrow();
        spot.setAvailable(true);
        availableSpots.get(ticket.getVehicleType()).offer(spot);

        return fee;
    }

    public void displayStatus() {
        System.out.println("\n--- Parking Lot Status ---");
        for (VehicleType type : VehicleType.values()) {
            long available = availableSpots.get(type).size();
            long total = allSpots.get(type).size();
            System.out.println(type + ": " + available + "/" + total + " spots available");
        }
        System.out.println("Active tickets: " + activeTickets.size());
    }
}
```

#### Main.java
```java
package com.example.parking;

public class Main {
    public static void main(String[] args) throws InterruptedException {
        ParkingLot lot = new ParkingLot();

        // Simulate parking
        ParkingTicket t1 = lot.parkVehicle("ABC123", VehicleType.CAR);
        System.out.println("Parked: " + t1.getTicketId() + " at spot " + t1.getSpotNumber());

        ParkingTicket t2 = lot.parkVehicle("XYZ789", VehicleType.BIKE);
        System.out.println("Parked: " + t2.getTicketId() + " at spot " + t2.getSpotNumber());

        lot.displayStatus();

        // Simulate exit after some time
        Thread.sleep(2000); // wait 2 seconds
        double fee = lot.exitVehicle(t1.getTicketId());
        System.out.println("\nExited " + t1.getTicketId() + ", fee: $" + fee);

        lot.displayStatus();
    }
}
```

**Explanation of collections used:**
- `availableSpots`: `ConcurrentHashMap<VehicleType, Queue<ParkingSpot>>` – thread-safe map where each vehicle type has a queue of available spots. We use `ConcurrentLinkedQueue` for thread-safe FIFO.
- `allSpots`: `ConcurrentHashMap<VehicleType, Set<ParkingSpot>>` – to keep all spots for a type. We use `Collections.synchronizedSet(new TreeSet<>())` to maintain sorted order and thread safety.
- `activeTickets`: `ConcurrentHashMap<String, ParkingTicket>` – thread-safe map for active tickets.
- `vehicleToTicket`: `ConcurrentHashMap<String, ParkingTicket>` – fast lookup by vehicle number.

---

### 10. Practice Exercises

1. **Implement an LRU Cache** using `LinkedHashMap`.
2. **Design a Task Scheduler** where tasks have priority; use `PriorityQueue`.
3. **Build a Simple In-Memory Database** for users (key: user ID, value: user object) using `HashMap`. Add thread safety.
4. **Simulate a Call Center** where calls are queued; use `ArrayDeque` as queue and multiple threads as agents (use `BlockingQueue`).
5. **Enhance the Parking Lot**:
   - Add `ConcurrentHashMap` for spot occupancy to quickly find which vehicle is in which spot.
   - Use `EnumMap` for storing rates per vehicle type.

---

### 11. Key Takeaways

- **Choose the right collection** based on your access patterns (order, duplicates, sorting, concurrency).
- **`ArrayList`** for random access, **`LinkedList`** for frequent insertions/deletions at ends.
- **`HashSet`** for fast uniqueness, **`TreeSet`** for sorted uniqueness.
- **`HashMap`** for key-value lookups, **`LinkedHashMap`** for predictable iteration, **`TreeMap`** for sorted keys, **`EnumMap`** for enum keys.
- **`PriorityQueue`** for priority-based processing.
- **`ArrayDeque`** for stack/queue operations.
- In multi-threaded LLD, use concurrent collections (`ConcurrentHashMap`, `CopyOnWriteArrayList`, `BlockingQueue`) or synchronize manually.
- Always consider performance characteristics (Big O) when designing for scale.

Collections are the bread and butter of LLD. Mastering them will enable you to implement complex systems efficiently. Tomorrow we'll start with our first design pattern – **Strategy Pattern** – and see how collections and enums fit into real-world designs.
