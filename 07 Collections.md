## Day 7 — Collections for Low-Level Design

In Java, the **Collections Framework** provides a set of interfaces and classes to store, retrieve, and manipulate groups of objects. Mastering these is essential for low-level design because they are the building blocks for managing data within your system. Each collection type serves a specific purpose, and choosing the right one can greatly simplify your design.

### Key Collections and Their Use Cases

- **List** – Ordered collection (sequence), allows duplicates, indexed access.  
  *Use when you need to maintain insertion order or access elements by position.*  
  Examples: `ArrayList` (fast random access), `LinkedList` (fast insertions/deletions).

- **Set** – Unordered collection, **no duplicates**.  
  *Use when you need to enforce uniqueness.*  
  Examples: `HashSet` (hash-based, fast), `TreeSet` (sorted order).

- **Queue** – Typically FIFO (first-in, first-out) order.  
  *Use for processing tasks in the order they arrive.*  
  Examples: `LinkedList` (can be used as a queue), `ArrayDeque`.

- **Map** – Key-value pairs.  
  *Use to look up values by a unique key (like an ID).*  
  Examples: `HashMap` (hash-based), `TreeMap` (sorted keys).

- **PriorityQueue** – A queue where elements are ordered according to their natural order or a custom `Comparator`.  
  *Use when you need to process items based on priority rather than insertion order.*  
  Example: `PriorityQueue` (heap-based).

---

### Problem Statement

Design a simple **Task Management System** that demonstrates the use of all these collections. The system should:

1. Store tasks by their unique ID using a **Map**.
2. Maintain a **List** of completed tasks (history) in the order they were finished.
3. Keep a **Queue** of pending tasks (to be processed in FIFO order).
4. Track unique **categories** of tasks using a **Set**.
5. Handle **urgent tasks** via a **PriorityQueue** where tasks are ordered by priority (higher priority first).

Each task has:
- `id` (int, unique)
- `title` (String)
- `category` (String)
- `priority` (int, higher number = higher priority)
- `status` (PENDING, COMPLETED)

The system should allow:
- Adding a new task (stores in map, adds to pending queue, adds category to set).
- Marking a task as completed (removes from pending, adds to history list, updates map).
- Viewing the next pending task (peek from queue).
- Viewing the next urgent task (peek from priority queue).
- Displaying all unique categories.

---

### Java Solution

```java
import java.util.*;

// Task class
class Task {
    int id;
    String title;
    String category;
    int priority;      // higher = more urgent
    String status;     // "PENDING" or "COMPLETED"

    public Task(int id, String title, String category, int priority) {
        this.id = id;
        this.title = title;
        this.category = category;
        this.priority = priority;
        this.status = "PENDING";
    }

    @Override
    public String toString() {
        return String.format("Task[%d] %s (%s) priority=%d status=%s",
                id, title, category, priority, status);
    }
}

// TaskManager using various collections
class TaskManager {
    // Map: id -> Task
    private Map<Integer, Task> taskMap = new HashMap<>();

    // Queue: pending tasks in FIFO order
    private Queue<Task> pendingQueue = new LinkedList<>();

    // PriorityQueue: urgent tasks (higher priority first)
    private Queue<Task> urgentQueue = new PriorityQueue<>(
            (t1, t2) -> Integer.compare(t2.priority, t1.priority) // descending
    );

    // Set: unique categories
    private Set<String> categories = new HashSet<>();

    // List: history of completed tasks (in completion order)
    private List<Task> completedHistory = new ArrayList<>();

    // Add a new task
    public void addTask(Task task) {
        if (taskMap.containsKey(task.id)) {
            System.out.println("Task ID already exists.");
            return;
        }
        taskMap.put(task.id, task);
        pendingQueue.offer(task);
        urgentQueue.offer(task);
        categories.add(task.category);
        System.out.println("Added: " + task);
    }

    // Mark a task as completed
    public void completeTask(int taskId) {
        Task task = taskMap.get(taskId);
        if (task == null) {
            System.out.println("Task not found.");
            return;
        }
        if ("COMPLETED".equals(task.status)) {
            System.out.println("Task already completed.");
            return;
        }
        // Update status
        task.status = "COMPLETED";
        // Remove from pending queues (we'll filter when polling)
        // Add to history
        completedHistory.add(task);
        System.out.println("Completed: " + task);
    }

    // View next pending task (FIFO)
    public void viewNextPending() {
        // Clean up queue: skip already completed tasks
        while (!pendingQueue.isEmpty() && "COMPLETED".equals(pendingQueue.peek().status)) {
            pendingQueue.poll();
        }
        Task next = pendingQueue.peek();
        System.out.println("Next pending (FIFO): " + (next == null ? "None" : next));
    }

    // View next urgent task (highest priority)
    public void viewNextUrgent() {
        while (!urgentQueue.isEmpty() && "COMPLETED".equals(urgentQueue.peek().status)) {
            urgentQueue.poll();
        }
        Task next = urgentQueue.peek();
        System.out.println("Next urgent: " + (next == null ? "None" : next));
    }

    // Show all unique categories
    public void showCategories() {
        System.out.println("Unique categories: " + categories);
    }

    // Show history of completed tasks
    public void showHistory() {
        System.out.println("Completed tasks history: " + completedHistory);
    }
}

// Demo class
public class CollectionsDemo {
    public static void main(String[] args) {
        TaskManager manager = new TaskManager();

        // Add tasks
        manager.addTask(new Task(1, "Fix bug", "Development", 5));
        manager.addTask(new Task(2, "Write docs", "Documentation", 3));
        manager.addTask(new Task(3, "Design review", "Development", 4));
        manager.addTask(new Task(4, "Respond to emails", "Communication", 2));

        System.out.println();

        // View next pending and urgent
        manager.viewNextPending();   // Should be task 1 (FIFO)
        manager.viewNextUrgent();    // Should be task 1 (priority 5)

        // Complete a task
        manager.completeTask(1);
        System.out.println();

        // View again
        manager.viewNextPending();   // Now task 2
        manager.viewNextUrgent();    // Now task 3 (priority 4, because task 1 is done)

        // Complete another
        manager.completeTask(3);
        System.out.println();

        manager.viewNextUrgent();    // Task 2 (priority 3)
        manager.viewNextPending();   // Task 2 (still FIFO)

        // Show categories and history
        System.out.println();
        manager.showCategories();
        manager.showHistory();
    }
}
```

**Output** (may vary slightly):
```
Added: Task[1] Fix bug (Development) priority=5 status=PENDING
Added: Task[2] Write docs (Documentation) priority=3 status=PENDING
Added: Task[3] Design review (Development) priority=4 status=PENDING
Added: Task[4] Respond to emails (Communication) priority=2 status=PENDING

Next pending (FIFO): Task[1] Fix bug (Development) priority=5 status=PENDING
Next urgent: Task[1] Fix bug (Development) priority=5 status=PENDING
Completed: Task[1] Fix bug (Development) priority=5 status=COMPLETED

Next pending (FIFO): Task[2] Write docs (Documentation) priority=3 status=PENDING
Next urgent: Task[3] Design review (Development) priority=4 status=PENDING
Completed: Task[3] Design review (Development) priority=4 status=COMPLETED

Next urgent: Task[2] Write docs (Documentation) priority=3 status=PENDING
Next pending (FIFO): Task[2] Write docs (Documentation) priority=3 status=PENDING

Unique categories: [Communication, Documentation, Development]
Completed tasks history: [Task[1] Fix bug (Development) priority=5 status=COMPLETED, Task[3] Design review (Development) priority=4 status=COMPLETED]
```

---

### Key Takeaways

- **Map** provides fast lookup by task ID.
- **List** preserves the order of completed tasks.
- **Queue** (LinkedList) maintains pending tasks in arrival order.
- **Set** ensures we don't duplicate categories.
- **PriorityQueue** allows us to retrieve the most urgent task efficiently.

By selecting the right collection for each responsibility, the design becomes clean, efficient, and easy to extend. Mastering these collections is crucial for any low-level design interview or real-world application development.
