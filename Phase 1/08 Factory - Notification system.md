Now we move to **Day 17 – Factory Method**, focusing on a notification system. The Factory Method pattern defines an interface for creating an object, but lets subclasses decide which class to instantiate. This allows adding new notification types without modifying existing code.

We'll implement a system where different notification factories produce specific notification objects: **Email**, **SMS**, and **Push**.

## Folder Structure

```
notification-factory-method/
└── src/
    └── main/
        └── java/
            └── com/
                └── example/
                    └── notification/
                        ├── Notification.java
                        ├── EmailNotification.java
                        ├── SMSNotification.java
                        ├── PushNotification.java
                        ├── NotificationFactory.java
                        ├── EmailFactory.java
                        ├── SMSFactory.java
                        ├── PushFactory.java
                        └── Main.java
```

---

## Code

### 1. `Notification.java` – Product Interface

```java
package com.example.notification;

public interface Notification {
    void send(String message);
}
```

### 2. Concrete Products

**EmailNotification.java**
```java
package com.example.notification;

public class EmailNotification implements Notification {
    @Override
    public void send(String message) {
        System.out.println("Sending Email: " + message);
    }
}
```

**SMSNotification.java**
```java
package com.example.notification;

public class SMSNotification implements Notification {
    @Override
    public void send(String message) {
        System.out.println("Sending SMS: " + message);
    }
}
```

**PushNotification.java**
```java
package com.example.notification;

public class PushNotification implements Notification {
    @Override
    public void send(String message) {
        System.out.println("Sending Push Notification: " + message);
    }
}
```

### 3. `NotificationFactory.java` – Abstract Creator

```java
package com.example.notification;

public abstract class NotificationFactory {
    // Factory method
    public abstract Notification createNotification();

    // Business logic that uses the factory method
    public void sendNotification(String message) {
        Notification notification = createNotification();
        notification.send(message);
    }
}
```

### 4. Concrete Factories

**EmailFactory.java**
```java
package com.example.notification;

public class EmailFactory extends NotificationFactory {
    @Override
    public Notification createNotification() {
        return new EmailNotification();
    }
}
```

**SMSFactory.java**
```java
package com.example.notification;

public class SMSFactory extends NotificationFactory {
    @Override
    public Notification createNotification() {
        return new SMSNotification();
    }
}
```

**PushFactory.java**
```java
package com.example.notification;

public class PushFactory extends NotificationFactory {
    @Override
    public Notification createNotification() {
        return new PushNotification();
    }
}
```

### 5. `Main.java` – Client

```java
package com.example.notification;

public class Main {
    public static void main(String[] args) {
        // Using Email factory
        NotificationFactory factory = new EmailFactory();
        factory.sendNotification("Hello via Email!");

        // Using SMS factory
        factory = new SMSFactory();
        factory.sendNotification("Hello via SMS!");

        // Using Push factory
        factory = new PushFactory();
        factory.sendNotification("Hello via Push!");
    }
}
```

**Output:**

```
Sending Email: Hello via Email!
Sending SMS: Hello via SMS!
Sending Push Notification: Hello via Push!
```

---

## Explanation

- **Product Interface (`Notification`)** – defines the behavior common to all notifications.
- **Concrete Products (`EmailNotification`, `SMSNotification`, `PushNotification`)** – each implements the `send()` method with its own logic.
- **Creator (`NotificationFactory`)** – declares the factory method `createNotification()` that returns a `Notification` object. It also contains business logic (`sendNotification`) that relies on the factory method.
- **Concrete Creators (`EmailFactory`, `SMSFactory`, `PushFactory`)** – override the factory method to instantiate the appropriate concrete product.

## Why Factory Method?

- **Open/Closed Principle** – adding a new notification type (e.g., `SlackNotification`) requires creating a new concrete product (`SlackNotification`) and a new concrete factory (`SlackFactory`). Existing code remains unchanged.
- **Encapsulation** – the creation logic is isolated from the client.
- **Flexibility** – the client can work with any factory that returns a `Notification`, without knowing the exact class.

This pattern is especially useful when the exact type of object to create is determined by subclasses, and when you want to decouple the client from the concrete classes.
