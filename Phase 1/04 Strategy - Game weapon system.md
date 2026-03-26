Below is a complete example of a **game weapon system** built with the Strategy Pattern. The system supports three weapons: **Sword**, **Gun**, and **Bow**. The player can switch between weapons at runtime, and each weapon has its own attack behavior. This pattern replaces conditional logic (like `if (weaponType == SWORD) ...`) with clean, extensible polymorphism.

## Folder Structure

```
weapon-system/
└── src/
    └── main/
        └── java/
            └── com/
                └── example/
                    └── weapon/
                        ├── WeaponStrategy.java
                        ├── Sword.java
                        ├── Gun.java
                        ├── Bow.java
                        ├── Player.java
                        └── Main.java
```

---

## File Contents

### 1. `WeaponStrategy.java` – Strategy Interface

```java
package com.example.weapon;

/**
 * The strategy interface declares the operation common to all
 * supported weapons.
 */
public interface WeaponStrategy {
    void attack();
}
```

**Explanation:**  
All weapon strategies must implement `attack()`, which defines how the weapon is used.

---

### 2. `Sword.java` – Concrete Strategy (Sword)

```java
package com.example.weapon;

public class Sword implements WeaponStrategy {
    @Override
    public void attack() {
        System.out.println("Slashing with sword! Deals 25 damage.");
    }
}
```

---

### 3. `Gun.java` – Concrete Strategy (Gun)

```java
package com.example.weapon;

public class Gun implements WeaponStrategy {
    @Override
    public void attack() {
        System.out.println("Shooting with gun! Deals 40 damage.");
    }
}
```

---

### 4. `Bow.java` – Concrete Strategy (Bow)

```java
package com.example.weapon;

public class Bow implements WeaponStrategy {
    @Override
    public void attack() {
        System.out.println("Firing arrow with bow! Deals 30 damage.");
    }
}
```

---

### 5. `Player.java` – Context

```java
package com.example.weapon;

/**
 * The context holds a reference to a WeaponStrategy and delegates
 * the attack action to it.
 */
public class Player {
    private WeaponStrategy currentWeapon;

    public void setWeapon(WeaponStrategy weapon) {
        this.currentWeapon = weapon;
    }

    public void attack() {
        if (currentWeapon == null) {
            System.out.println("No weapon equipped! Punch deals 5 damage.");
        } else {
            currentWeapon.attack();
        }
    }
}
```

**Explanation:**  
The `Player` class is the context. It maintains a reference to a `WeaponStrategy` (the currently equipped weapon). When `attack()` is called, it delegates the attack behavior to the current weapon. The player doesn’t need to know which weapon it is using – polymorphism takes care of it.

---

### 6. `Main.java` – Client Code

```java
package com.example.weapon;

public class Main {
    public static void main(String[] args) {
        Player player = new Player();

        // Equip sword and attack
        player.setWeapon(new Sword());
        player.attack();  // Slashing with sword! Deals 25 damage.

        // Switch to gun
        player.setWeapon(new Gun());
        player.attack();  // Shooting with gun! Deals 40 damage.

        // Switch to bow
        player.setWeapon(new Bow());
        player.attack();  // Firing arrow with bow! Deals 30 damage.
    }
}
```

**Output:**

```
Slashing with sword! Deals 25 damage.
Shooting with gun! Deals 40 damage.
Firing arrow with bow! Deals 30 damage.
```

---

## How the Pattern Replaces If-Else

Without the Strategy Pattern, the `Player` class might contain a mess of conditionals:

```java
public void attack() {
    if (weaponType.equals("SWORD")) {
        System.out.println("Slashing with sword! Deals 25 damage.");
    } else if (weaponType.equals("GUN")) {
        System.out.println("Shooting with gun! Deals 40 damage.");
    } else if (weaponType.equals("BOW")) {
        System.out.println("Firing arrow with bow! Deals 30 damage.");
    } else {
        System.out.println("Punch deals 5 damage.");
    }
}
```

Every time a new weapon is added, the `attack()` method would need to be modified – violating the Open/Closed Principle. With the Strategy Pattern, adding a new weapon (e.g., `Axe`) simply means creating a new class that implements `WeaponStrategy`. The `Player` class remains unchanged.

---

## How the Components Work Together

- **Strategy Interface (`WeaponStrategy`)** – defines the common attack behavior.
- **Concrete Strategies (`Sword`, `Gun`, `Bow`)** – each implements its own attack logic.
- **Context (`Player`)** – holds a reference to the current strategy and delegates the attack.
- **Client (`Main`)** – selects which strategy to use and sets it on the context.

---

## Benefits

- **Open/Closed Principle** – new weapons can be added without modifying existing code.
- **Single Responsibility** – each weapon class encapsulates its own behavior; `Player` focuses on player state and delegation.
- **Runtime Flexibility** – weapons can be swapped dynamically (e.g., when the player picks up a new weapon).
- **Testability** – each weapon strategy can be unit‑tested in isolation.

This pattern is widely used in game development to handle interchangeable behaviors like weapons, movement, or AI states.
