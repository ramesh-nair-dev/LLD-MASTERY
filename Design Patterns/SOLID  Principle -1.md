# SOLID  Principle -1

---

## What are Principles?

A **principle** is nothing but a good practice that should be followed.

> Think of it like Jon Snow's moral code—he never betrays and always acts with honor. This is a principle he follows, not a rule he must follow.
> 

On the other hand, **rules** are strict boundaries that must be followed. For example:

> "You should not drink and drive" – this is a rule.
> 

Just like this, in programming, we have **SOLID Principles**, which are recommended best practices for designing clean, scalable, and maintainable software. These are not compulsory, but they are highly encouraged.

---

## SOLID Principles (Focus: SRP and OCP)

Let’s take an example: Suppose we are given a problem to design a `Bird` class.

### Version 0: Initial Bird Design

```java
class Bird {
    String name;
    String type;
    String wings;
    String gender;

    void makeSound() {}
    void eat() {}
    void fly() {}
}

```

Now let’s instantiate and use it:

```java
Bird b1 = new Bird();
b1.type = "Sparrow";

Bird b2 = new Bird();
b2.type = "Eagle";

b1.makeSound();  // should make sparrow sound
b2.makeSound();  // should make eagle sound

```

Let’s implement `makeSound()` like this:

```java
void makeSound() {
    if (this.type.equals("Eagle")) {
        // Eagle sound logic
    } else if (this.type.equals("Sparrow")) {
        // Sparrow sound logic
    }
}

```

### Problem Here? ❌ Violation of SRP

### SRP – Single Responsibility Principle

> Every code unit should have one responsibility to take care of.
> 
> 
> There should be only **one reason** to change a code unit.
> 

Let’s break this down further:

- In the `makeSound()` method above, we are branching based on `type` and putting bird-specific sound logic right there.
- Similarly, if we do the same in `fly()`, the class now handles flying logic for different bird types as well.

So `Bird` class now has **multiple responsibilities**:

1. Controlling sound logic for different birds.
2. Controlling flying logic for different birds.

Any change in either logic (say eagle’s fly behavior) requires a change in the `Bird` class itself. This means **multiple reasons to change**, hence SRP is violated.

---

### Another SRP Violation Example

```java
class Invoice {
    void calculateTax() {
        // Direct tax logic written here
    }

    void printInvoice() {
        // Direct print logic written here
    }

    void giveDiscount() {
        // Direct discount logic written here
    }
}

```

This class has **three separate responsibilities**, and changing any of them will require modifying the `Invoice` class. ❌ SRP is violated.

---

### Fixing the SRP Violation

```java
class Invoice {
    void calculateTax() {
        taxCalculator.calculate();
    }

    void printInvoice() {
        printer.print();
    }

    void giveDiscount() {
        discountEngine.apply();
    }
}

```

Now:

- Logic is delegated to external classes/methods.
- `Invoice` no longer needs to change when logic changes elsewhere. ✅ SRP is followed.

---

### How to Identify SRP Violation:

1. **Multiple if-else blocks with direct logic inside**:

```java
if (i == 1) {
    // logic to play music
} else if (i == 2) {
    // logic to setup DB
}

```

Multiple unrelated logic → multiple reasons to change → ❌ SRP Violation

✅ Instead:

```java
if (i == 1) {
    playMusic();  // logic elsewhere
} else if (i == 2) {
    setUpDb();    // logic elsewhere
}

```

1. **Monster methods doing too much**:

```java
void afterPurchase() {
    // order confirmation logic
    // inventory management logic
    // notify seller
    // notify buyer
}

```

This has **4 different responsibilities**. If any one changes, this method must change. ❌ SRP violated

---

## OCP – Open/Closed Principle

> Your code should be open for extension but closed for modification.
> 

What does this mean?

- You should be able to add **new functionality** without changing **existing code**.

**Bad Practice**:

You are asked to build a new feature but you also edit other unrelated files. This introduces risk and tight coupling → bugs & side effects.

---

### Back to Bird Design – Fixing via OCP

We redesign the Bird class as an **abstract class**:

```java
abstract class Bird {
    abstract void fly();
    abstract void makeSound();
}

// we will also have all data members like name , type etc

```

Then we create specific bird classes:

```java
class Eagle extends Bird {
    void fly() {
        // Eagle's fly logic
    }
    void makeSound() {
        // Eagle's sound
    }
}

class Parrot extends Bird {
    void fly() {
        // Parrot fly logic
    }
    void makeSound() {
        // Parrot sound
    }
}

```

### Why Not Interface?

- Because we have an **is-a** relationship: Eagle *is a* Bird.
- Also, child classes need **data members** access, which abstract classes support (but interfaces do not).
- Interfaces are best when there’s **no is-a relationship** and only behavior needs to be defined.

✅ Now:

- You can add new birds without changing the `Bird` class.
- `Bird` is **open for extension** (add new birds).
- `Bird` is **closed for modification** (base class doesn’t change).

---

## Final Thoughts:

- SRP = One responsibility, one reason to change
- OCP = Add new features without touching existing code
- If your class/method changes too often for different reasons, you need to **refactor for SRP**.
- If adding something new causes ripple changes elsewhere, refactor to follow **OCP**.

---

These principles help in building scalable and maintainable codebases – like a true elite software engineer or a great computer scientist would.