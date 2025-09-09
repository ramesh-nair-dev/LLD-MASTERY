# Interface & Abstract class

---

### 🔹 Object: State & Behavior

- Every **object** in Java has two primary components:
    - **State** → Represented by its data members (variables)
    - **Behavior** → Represented by its methods (functions)

🧠 **Key Insight**: If two objects belong to the **same class**, they are differentiated by their **state** (i.e., values in their data members).

---

### 🔹 Inheritance (IS-A Relationship)

### ✅ Valid Example:

```java
class Animal {}
class Dog extends Animal {}
class Cat extends Animal {}

```

- **Dog is-a Animal** ✅
- **Cat is-a Animal** ✅

When there is an **IS-A** relationship, we use **inheritance**.

### ❌ Invalid Example:

```java
class RoboticDog // NOT a real Animal

```

- Even though `RoboticDog` can `walk()`, just like a real `Dog`, it is **not a real animal**, hence:
- ❌ `RoboticDog` should **not** inherit from `Animal`
- Inheritance creates **tight coupling**. So only use it when there's a **true IS-A** relationship.

---

### 🔹 Grouping by Behavior → Use **Interfaces**

```java
interface Runner {
    void runner(); // implicitly public & abstract
}

```

- Interface is a **contract**.
- Any class that **implements** the interface must **override** its methods.
- Interfaces contain **abstract methods** by default.

---

### 🔹 Implementing Interface in Class

```java
class Dog implements Runner {
    public void runner() {
        System.out.println("Running");
    }
}

```

⚠️ If you do:

```java
void runner() // instead of public void runner()

```

This will throw an **error**. You can't reduce visibility (from `public` to default) 

---

### 🔹 Polymorphism with Interfaces

```java
Runner r1 = new Dog();
r1.runner();

```

- Compiler checks: Does `Runner` have a `runner()` method? ✅ Yes
- Runtime: Object is of type `Dog`, so `Dog`'s `runner()` is executed.

👨‍🏫 Analogy:

- Think of Interface as **parent**, and implementing class as **child**.
- Parent reference can point to child object.

---

### 🔹 Real-Life Analogy: PhonePe with Banks

### 🔸 Problem:

```java
YesBank yb = new YesBank();
yb.getBalance();
yb.transferMoney();

```

- If `YesBank` crashes, and you switch to `ICICIBank` (which has different methods: `checkBalance()`, `moneyTransfer()`),
- You will have to change **all code references** → time consuming and error-prone.

### ✅ Solution:

Use Interface:

```java
interface Bank {
    void getBalance();
    void transferMoney();
}

class YesBank implements Bank {
    // Implementation
}

class ICICIBank implements Bank {
    // Implementation
}

// by doing these both bank should override the function metioned in interace 

```

Then simply use:

```java
Bank b = new YesBank();
b.getBalance();

// Switch to ICICIBank
Bank b = new ICICIBank();
b.getBalance();

```

- ✅ Just **change one line**, everything else works!

---

### 🔹 @Override Annotation

```java
class A {
    public void fun() {}
}

class B extends A {
    @Override
    public void fun() {}

    @Override
    public void fun1() {} // ❌ Error: fun1 is not in parent class
}

```

- `@Override` helps **verify** if you're correctly overriding a parent method.
- If no such method in parent, you'll get a **compile-time error**.
- Without `@Override`, the method is just treated as a new method.

✅ **Best Practice**: Always use `@Override` when intending to override.

---

### 🔹 Why Java Doesn't Support Multiple Inheritance in Classes

```java
class A {}
class B extends A { void xyz() {} }
class C extends A { void xyz() {} }

class D extends B, C {} // ❌ Error: Ambiguity in xyz()

```

- If class `D` inherits both `B` and `C`, and both have `xyz()`, Java won't know which one to call.
- ❌ Hence Java **doesn't allow multiple class inheritance**.

✅ Interfaces Solve This:

```java
interface B { void xyz(); }
interface C { void xyz(); }

class D implements B, C {
    public void xyz() {
        // Own implementation
    }
}

D d = new D();
d.xyz(); // ✅ Works fine

// it will work fine because the class are implementing interface method in interface    function must be overriden , so d will call it's own xyz function at runtime
```

---

### 🔹 Problem: Adding New Method in Interface Used by 50 Classes

```java
interface A {
    void a();
}

// 50 classes implement A, each has its own a()

// Now we want to add:
void abc(); // ❌ All 50 classes break until they implement abc()

```

✅ Solution: Use `default` methods in interfaces

```java
interface A {
    void a();
    default void abc() {
        // default implementation
    }
}

```

- Child classes are **not required** to override `abc()` unless they want custom behavior.

---

### 🔹 Abstract Class

- Similar to a normal class but with 2 key differences:
    1. Can have **abstract methods** (without body)
    2. Cannot be instantiated directly

```java
abstract class Animal {
    abstract void makeSound(); // Can have or not have abstract methods
}

Animal a = new Animal(); // ❌ Cannot instantiate

```

✅ A class with `abstract` keyword:

- Can have **concrete methods** (with body)
- Can have **abstract methods**
- Meant to be **extended**, not instantiated directly

---

### ✅ Final Summary

| Concept | Use Case |
| --- | --- |
| Inheritance | Use when there's a true IS-A relationship |
| Interface | Group objects by common behavior, avoid tight coupling |
| Abstract Class | When you want to enforce partial implementation in subclasses |
| @Override | Helps confirm correct method overriding |
| Default Methods in Interface | Add new method without breaking existing implementations |

You're thinking like a top-tier engineer now 🚀