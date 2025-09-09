# Factory & Abstract Factory Design Patterns

## 🧠 Why Factory Patterns Exist — The Deep Reason

When you write code that **creates objects based on input**, you face 3 major software design problems:

---

## 🧪 Raw Case Study: User Types in an Application

```java
abstract class User {}
class Student extends User {}
class Teacher extends User {}
class Admin extends User {}

```

### 🚫 Naive Object Creation

```java
String userType = getUserType(); // API or user input
User user;

if (userType.equals("Student")) {
    user = new Student();
} else if (userType.equals("Teacher")) {
    user = new Teacher();
} else if (userType.equals("Admin")) {
    user = new Admin();
}

```

### ❌ Problems:

- **SRP Violation (Single Responsibility Principle)** – Your client code is doing two things:
    - Running business logic
    - Deciding *how* to create the right object
- **OCP Violation (Open-Closed Principle)** – If you add a new type (e.g., `Mentor`), you must edit all if-else/switch-case blocks everywhere. This is fragile and error-prone.
- **Code Duplication** – You duplicate object creation logic across multiple places, making code harder to maintain.

---

## ✅ Solution 1: Simple Factory Pattern

We extract the object creation logic into a dedicated class.

```java
class UserFactory {
    public static User createUser(String type) {
        switch(type) {
            case "Student": return new Student();
            case "Teacher": return new Teacher();
            case "Admin": return new Admin();
            default: throw new IllegalArgumentException("Invalid type");
        }
    }
}

```

### Client Code:

```java
User user = UserFactory.createUser(type);

```

### ✅ Benefits:

- **SRP fixed**: Client no longer handles creation logic.
- **Reusable**: All creation logic is centralized.

### ⚠️ Still not perfect:

- **OCP is still violated**: Adding `Mentor` still requires changing the `UserFactory`.
- Still uses `switch` or `if-else`, so extensibility is limited.

---

## ✅✅ Solution 2: Factory Method Pattern

This solves **OCP** by moving object creation into separate factory subclasses.

### 🧱 Structure:

```java
interface Product {}
class ProductA implements Product {}
class ProductB implements Product {}
class ProductC implements Product {}

interface ProductFactory {
    Product createProduct();
}

class ProductAFactory implements ProductFactory {
    public Product createProduct() { return new ProductA(); }
}
class ProductBFactory implements ProductFactory {
    public Product createProduct() { return new ProductB(); }
}

```

### 🔍 Behind the Scenes:

- `ProductFactory` is an interface.
- Each subclass returns one specific product.
- **Client only depends on the interface**.

### 🎯 Client Code:

```java
ProductFactory factory = new ProductAFactory();
Product product = factory.createProduct();

```

### 🧠 Why this works:

- Adding new types doesn't affect old code.
- Each new product gets its own new factory — **no changes required** in client.
- Polymorphism ensures correct method resolution at runtime.

---

## 🕹 Example: Game Obstacles (Factory Method in Action)

```java
interface Obstacle {}
class Animal implements Obstacle {}
class Plant implements Obstacle {}
class Zombie implements Obstacle {}

interface ObstacleFactory {
    Obstacle createObstacle();
}

class BalancedObstacleFactory implements ObstacleFactory {
    public Obstacle createObstacle() {
        // Logic to alternate Animal, Plant, Zombie
        return new Animal();
    }
}

class ImbalancedObstacleFactory implements ObstacleFactory {
    public Obstacle createObstacle() {
        // Random logic for harder obstacles
        return new Zombie();
    }
}

```

### 🧪 Client Logic:

```java
ObstacleFactory factory;
if (level <= 2)
    factory = new BalancedObstacleFactory();
else
    factory = new ImbalancedObstacleFactory();

Obstacle ob = factory.createObstacle();

```

---

## 🧰 Abstract Factory Pattern

Abstract Factory is used when you need to create **families of related objects** (e.g., UI toolkits for Android vs iOS).

### Example:

```java
abstract class Button {}
class IOSButton extends Button {}
class AndroidButton extends Button {}

abstract class CheckBox {}
class IOSCheckBox extends CheckBox {}
class AndroidCheckBox extends CheckBox {}

```

### 🎯 Problem:

If we use `IOSButton` with `AndroidCheckbox`, it creates inconsistent UI behavior.

We need a factory that gives us **all components of one ecosystem**.

### 🧱 Abstract Factory Structure:

```java
interface WidgetFactory {
    Button createButton();
    CheckBox createCheckBox();
}

class IOSFactory implements WidgetFactory {
    public Button createButton() { return new IOSButton(); }
    public CheckBox createCheckBox() { return new IOSCheckBox(); }
}

class AndroidFactory implements WidgetFactory {
    public Button createButton() { return new AndroidButton(); }
    public CheckBox createCheckBox() { return new AndroidCheckBox(); }
}

```

### 🧪 Client Code:

```java
WidgetFactory factory = new IOSFactory();
Button btn = factory.createButton();
CheckBox cb = factory.createCheckBox();

```

### ✅ Benefits:

- Produces consistent families of objects.
- **OCP respected**: You can add new families (e.g., WindowsFactory) without changing old code.
- Encapsulation of theme, OS, platform behaviors.

---

## 🤯 Summary Table

| Pattern | Solves SRP | Solves OCP | Creates Families | Suitable For |
| --- | --- | --- | --- | --- |
| Naive if-else | ❌ | ❌ | ❌ | Quick scripts |
| Simple Factory | ✅ | ❌ | ❌ | Small apps |
| Factory Method | ✅ | ✅ | ❌ | Extensible logic |
| Abstract Factory | ✅ | ✅ | ✅ | UI kits, themes |

---

##