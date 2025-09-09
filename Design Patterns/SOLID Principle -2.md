# SOLID Principle -2

---

### 🦜 Bird Design Evolution to Understand SOLID Principles

We'll go through multiple versions of bird class designs to understand the SOLID principles

---

## ✅ Version 1 — Initial Abstract Bird Class

```java
abstract class Bird {
    // we will also have some data member here
    abstract void fly();
    abstract void makeSound();
}

```

> As abstract classes cannot be instantiated, we create concrete subclasses.
> 

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

- Eagle and Parrot both extend `Bird`, so they must implement `fly()` and `makeSound()`.
- This removes the `if-else` checks that violated the **Open-Closed Principle (OCP)** in earlier versions.

> 🧠 But what if we add a new bird — Penguin?
> 

```java
class Penguin extends Bird {
    void fly() {
        // leave empty OR throw exception — not ideal!
    }
    void makeSound() {
        // Penguin sound
    }
}

```

🔴 **Problem**: Penguins can’t fly, but we’re forcing them to implement a method they shouldn’t use. This violates **Liskov Substitution Principle (LSP) i.e (Parent class wants child class implement the fly method but child class Penguin say i don’t know  to fly how will i implement it this is called  LSP)**.

---

## ✅ Version 2 — FlyingBird vs NonFlyingBird Split

```java
abstract class Bird {
    // Common data members like name, type, gender, etc.
    abstract void makeSound();
}

abstract class FlyingBird extends Bird {
    abstract void fly();
}

abstract class NonFlyingBird extends Bird {
    // No fly method here
}

```

```java
class Eagle extends FlyingBird {
    void fly() {
        // fly logic
    }
    void makeSound() {
        // eagle sound
    }
}

class Penguin extends NonFlyingBird {
    void makeSound() {
        // penguin sound
    }
}

```

✅ Now Penguin is not forced to implement `fly()` — clean design.

🔴 **New Problem**: Class Explosion — if we add swimming and dancing as behaviors, we get combinations:

- Fly + Swim
- Fly + Dance
- NonFly + Swim
- NonFly + Dance
- ... etc (2n classes)

---

## ✅ Version 3 — Interfaces for Behavior (Preferred Design)

Do all birds fly? ❌

Do all birds make sound? ✅

Do all birds have breed/wings/gender? ✅

Thus, Bird class should only include **universal properties**.

```java
abstract class Bird {
    // Common properties: gender, wings, breed
    abstract void makeSound();
}

```

Now for flying birds:

```java
interface Flyable {
    void fly();
}

```

```java
class Eagle extends Bird implements Flyable {
    void fly() {
        // fly logic
    }
    void makeSound() {
        // eagle sound
    }
}

class Penguin extends Bird {
    void makeSound() {
        // penguin sound
    }
    // no fly method needed
}

```

🦚 What if Peacock can **fly** and **dance**?

```java
interface Danceable {
    void dance();
}

class Peacock extends Bird implements Flyable, Danceable {
    void fly() {
        // peacock fly logic
    }
    void dance() {
        // peacock dance logic
    }
    void makeSound() {
        // peacock sound
    }
}

```

✅ This avoids forcing non-dancing birds to implement `dance()` — maintaining SRP & ISP.

---

## ✅ Interface Segregation Principle (ISP)

```java
// Not good
interface FlyDance {
    void fly();
    void dance();
}

```

Why is this bad?

- A bird might fly but not dance.
- This forces unrelated methods together.

✅ Better:

```java
interface Flyable {
    void fly();
}

interface Danceable {
    void dance();
}

```

---

## ✅ Dependency Inversion Principle (DIP)

**Problem**: Multiple birds use the same flying logic:

```java
class Eagle implements Flyable {
    void fly() {
        // fly high
    }
}

class Vulture implements Flyable {
    void fly() {
        // fly high
    }
}

class Sparrow implements Flyable {
    void fly() {
        // fly low
    }
}

```

🔴 Repetition! Let’s abstract the algorithm.

```java
class FlyHigh {
    void flyHigh() {
        // algorithm for flying high
    }
}

class FlyLow {
    void flyLow() {
        // algorithm for flying low
    }
}

```

```java
class Eagle implements Flyable {
    FlyHigh fh = new FlyHigh();
    void fly() {
        fh.flyHigh();
    }
}

```

🔴 But now Eagle is tightly coupled to `FlyHigh`. If we want to change behavior (e.g. to `FlyHigh20`), we must change it in many places — violation of **DIP**.

✅ Solution: Use interfaces to decouple behavior.

```java
interface FlyBehavior {
    void makeFly();
}

class FlyHigh implements FlyBehavior {
    public void makeFly() {
        // high fly algo
    }
}

class FlyLow implements FlyBehavior {
    public void makeFly() {
        // low fly algo
    }
}

```

```java
class Eagle implements Flyable {
    FlyBehavior flyBehavior = new FlyHigh();
    void fly() {
        flyBehavior.makeFly();
    }
}

```

Now, if we want Eagle to fly at mid-height:

```java
class FlyMid15 implements FlyBehavior {
    public void makeFly() {
        // fly mid algo
    }
}

// update behavior
Eagle.flyBehavior = new FlyMid15();

```

✅ Just one change needed — this follows **Dependency Inversion**.

---

### ✅ Summary of Principles Demonstrated

| Principle | How It's Applied |
| --- | --- |
| SRP | Each class/interface has one responsibility (Bird, Flyable, Danceable) |
| OCP | New behaviors (e.g. swim) added via interfaces, no modification needed in existing code |
| LSP | Subclasses (e.g. Penguin) honor contracts of base classes/interfaces |
| ISP | Interfaces are kept small and specific (Flyable vs FlyDance) |
| DIP | Classes depend on abstractions (FlyBehavior) not concrete implementations (FlyHigh) |

---

📌 **Final Thought**:

> Design based on behavior composition via interfaces instead of deep inheritance. This avoids class explosion, respects SOLID principles, and keeps code flexible and scalable.
>