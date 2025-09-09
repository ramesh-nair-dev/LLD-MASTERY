# Decorator and Flyweight

---

These are **structural design patterns**, which means they help in organizing and structuring code to make it more maintainable, extensible, and readable. We’ll cover:

1. Decorator Pattern (with the Starbucks Coffee example)
2. Flyweight Pattern (with the PUBG bullet example)

Each section explains not only the “how” but the **“why” behind every design decision**, focusing on **internal memory behavior, abstraction levels, and extensibility**.

---

## ☕ Decorator Design Pattern

### 🔍 Definition:

Decorator pattern allows you to **dynamically add new behavior or responsibilities** to objects **without modifying their structure or class**. You wrap the object inside another object, i.e., a decorator, that adds the behavior.

> Think of a decorator as a wrapper around an object that preserves the type but adds something extra.
> 

---

### 🧠 Real-life Analogy:

Suppose you're gifting a toy car:

- The toy car = base object
- You put it in a box = one wrapper
- You wrap the box with gift paper = another wrapper
- You tie a ribbon and add a name slip = further wrappers

But the **underlying object is still a toy car**, just decorated. This is exactly how decorator works.

---

### ☕ Starbucks Coffee Example:

In Starbucks, you can order basic coffee and customize it with add-ons like milk, mocha, whip, etc.

### Step 1: Base Class (Beverage)

```java
public abstract class Beverage {
    public abstract void getDesc();
    public abstract int getCost();
}

```

Now we create concrete coffees:

```java
public class DarkRoast extends Beverage {
    public void getDesc() { System.out.println("Dark Roast"); }
    public int getCost() { return 100; }
}

```

You can do:

```java
Beverage b = new DarkRoast();
System.out.println(b.getCost()); // 100

```

But what if you want: `DarkRoast + Milk + Mocha`?

### 🔥 Naive Idea:

Create combinations like `DarkRoastWithMilkMocha`. But this leads to **class explosion** – unmanageable.

---

### ✅ Best Design: Decorator Pattern

### 🎯 Key Insight:

We wrap `Beverage` objects with other objects that add features but **preserve the type**.

### Step 2: Abstract Decorator (AddOn)

```java
public abstract class AddOn extends Beverage {
    Beverage b;
    public AddOn(Beverage b) {
        this.b = b;
    }
}

```

Now the AddOns:

```java
public class Milk extends AddOn {
    public Milk(Beverage b) { super(b); }
    public void getDesc() {
        b.getDesc();
        System.out.println("+ Milk");
    }
    public int getCost() {
        return b.getCost() + 2;
    }
}

public class Mocha extends AddOn {
    public Mocha(Beverage b) { super(b); }
    public void getDesc() {
        b.getDesc();
        System.out.println("+ Mocha");
    }
    public int getCost() {
        return b.getCost() + 20;
    }
}

```

### Step 3: Client Code

```java
Beverage b = new DarkRoast();           // DarkRoast @ 10k
b = new Milk(b);                        // Milk @ 20k with b → 10k
b = new Milk(b);                        // Milk @ 30k with b → 20k
b = new Mocha(b);                       // Mocha @ 40k with b → 30k
System.out.println(b.getCost());       // Output: 100 + 2 + 2 + 20 = 124

```

### 🧠 Behind the Scenes (Memory):

- Each wrapper **adds cost** and **calls the wrapped object**.
- **Stack** stores the reference variable `b`, but actual objects live in **Heap**.
- On calling `getCost()`, the method call chain walks down the stack of decorators until it hits the base object.

---

### 📊 UML Diagram – Decorator Pattern

```
          Beverage
             ↑
 ┌──────────┴─────────────┐
 │                        │
DarkRoast           AddOn (abstract)
                         ↑
          ┌──────────────┴─────────────┐
       Milk                        Mocha

```

---

## 🎯 Flyweight Design Pattern

### 🔍 Definition:

Flyweight pattern is used to **minimize memory usage** by sharing as much data as possible with similar objects. It separates **intrinsic (shared)** and **extrinsic (non-shared)** data.

---

### 🎮 PUBG Bullet Example:

**Scenario:**

- 100 players × 2 guns × 150 bullets = 300 bullets per player × 100 = **30,000 bullets**
- Each bullet ≈ 1.1 KB → 30,000 bullets = **33 MB RAM** usage!

---

### 💡 Optimization Using Flyweight

Instead of creating separate memory for each bullet:

### 🎯 Identify Intrinsic vs Extrinsic

- **Intrinsic (Shared)**: diameter, weight, texture, range, speed, bullet image
- **Extrinsic (Unshared)**: current position, velocity, target, direction

### 💡 We store intrinsic data once and pass extrinsic data during rendering.

```java
class BulletType {
    String texture;
    int diameter;
    int range;
    // shared values
}

class BulletContext {
    int x, y, dx, dy;
    BulletType bulletType; // shared flyweight
}

```

This way we only store 2–3 instances of BulletType (e.g., 7.62mm, 5.56mm) and reuse them.

---

### 📊 UML Diagram – Flyweight Pattern

```
      BulletType (Flyweight)
            ↑
 ┌──────────┐
 │  Factory │─── getBullet(type) ──> returns shared instance
 └──────────┘

BulletContext (x, y, direction, BulletType)

```

---

### 🧠 Benefits

| Pattern | Core Idea | Benefit |
| --- | --- | --- |
| Decorator | Add responsibilities dynamically by wrapping | Avoid class explosion, increase flexibility |
| Flyweight | Share common parts of state to save memory | Huge memory reduction |

---

## ✅ Summary

- Decorator gives **runtime flexibility** in behavior extension.
- Flyweight gives **memory efficiency** by sharing objects.
- Both follow **composition over inheritance**.

These patterns become critical when designing scalable, clean systems – especially for UI rendering, game engines, or microservices where **extensibility and performance** matter.

---