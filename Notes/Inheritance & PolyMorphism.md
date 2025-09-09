# Inheritance & PolyMorphism

**Inheritance in Object-Oriented Programming**

### **1. Understanding Inheritance**

- Inheritance establishes a **parent-child relationship** where the **child class** inherits all properties and methods from the **parent class**.
- The child class **can have its own additional properties and methods** that are not accessible by the parent class.
- **"Is-a" Relationship**: Inheritance applies when an object **is a type of another object**.
    - **Example**: A dog **is an** animal, a cat **is an** animal.
    - **Counter-example**: A robot **can walk**, and an animal **can walk**, but a robot **is not an** animal.
- Moving **from parent to child** leads to **specialization**, while moving **from child to parent** leads to **generalization**.

### **2. Implementing Inheritance in Java**

```java
class A {
    int prop1;
    int prop2;
}

class B extends A {
    int prop3;
    // This class has access to all properties and methods of Class A.
}

```

---

### **3. Constructor Overloading & Chaining**

- Instead of writing multiple constructors with redundant code, we use **constructor chaining** with `this()`.
- **Optimized Approach:**

```java
class A {
    int a, b, c;

    A() {
        this(0, 0, 0); // Calls A(int, int, int)
    }

    A(int a, int b) {
        this(a, b, 0); // Calls A(int, int, int)
    }

    A(int b, int c) {
        this(0, b, c); // Calls A(int, int, int)
    }

    A(int a, int b, int c) {
        this.a = a;
        this.b = b;
        this.c = c;
    }
}

```

- **Key Points:**
    - `this()` calls another constructor from the same class.
    - It must always be the **first line** in a constructor.

---

### **4. Constructor Execution in Multi-Level Inheritance**

```java
class A {
    A() {
        System.out.println("A");
    }
}

class B extends A {
    B() {
        System.out.println("B");
    }
}

class C extends B {
    C() {
        System.out.println("C");
    }
}

class D extends C {
    D() {
        System.out.println("D");
    }
}

```

### **Flow of Execution:**

```java
D d = new D();

```

**Output:**

```
A
B
C
D

```

**Explanation:**

1. **D()** constructor calls **C()**.
2. **C()** constructor calls **B()**.
3. **B()** constructor calls **A()** (parent).
4. Since **A is the top-most parent**, it prints first.
5. Then the execution flows back down to **D**, printing each class in order.

---

### **5. Using `super()` for Parent Constructors**

- `super()` calls the parent class constructor.
- If `super(x)` is used in **C**, it will invoke a **specific constructor in B**.
- `super()` and `this()` **cannot be used together in the same constructor**.

---

### **6. Object Creation & Reference Type Rules**

### **Case 1: Parent Reference to Child Object**

```java
Animal a = new Dog(); // ✅ Allowed

```

- A **Dog object** is created with **Animal properties**.
- `a.prop1` (Animal) → ✅ **Accessible because compiler see a.prop1 reference is of animal it will check hey for animal reference do we have prop1  property yes it will pass**
- `a.prop2` (Animal) → ✅ **Accessible**
- `a.prop3` (Dog) → ❌ **Not Accessible** (because `a` is of type `Animal`). this will be executed in runtime after the object is created

### **Case 2: Child Reference to Parent Object**

```java
Dog d = new Animal(); // ❌ Not Allowed

```

- **Compiler Error:** A child class cannot reference a parent object.
- At **compile-time**, `d` expects `prop3`, but an `Animal` object does not have  at runtime it will give runtime error that’s why java we don’t write child reference to parent runtime error are hard to track

---

### **7. Method Overloading vs. Overriding**

### **Method Overloading (Compile-time Polymorphism)**

```java
class Example {
    void hello() {}
    void hello(int x) {}
    void hello(int x, int y) {}
    void hello(String x) {}
}

```

- **Decided at compile time** based on **method signature (name + parameters)**.

### **Method Overriding (Runtime Polymorphism)**

```java
class A {
    void hello() { System.out.println("Hello from A"); }
}

class B extends A {
    void hello() { System.out.println("Hello from B"); }
}

public class Main {
    public static void main(String[] args) {
        A b = new B();
        b.hello(); // Output: "Hello from B"
																										    
    }
}

```

- **Decided at runtime** based on the **object type, not reference type**.
- **Use case:** Different implementations in child classes.

---

### **8. Real-World Example: User Management System**

```java
class User {
    void login() { System.out.println("User Login"); }
}

class TA extends User {}
class Student extends User {
    void login() { System.out.println("Student Login"); }
}
class Mentor extends User {}

public class Main {
    static void loginHelper(User u) {
        u.login();
    }

    public static void main(String[] args) {
        loginHelper(new TA());       // "User Login"
        loginHelper(new Student());  // "Student Login"
        loginHelper(new Mentor());   // "User Login"
    }
}

```

**Explanation:**

- If `login()` is **not overridden**, the **parent class method** executes.
- If `login()` **is overridden**, the **child class method** executes (e.g., `Student`).

---

###