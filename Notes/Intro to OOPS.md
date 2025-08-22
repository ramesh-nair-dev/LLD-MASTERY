# Intro to OOPS

**LLD Lecture 1: Class & Object, OOP Concepts**

## **1️⃣ Class & Object**

Imagine:

- **Jon is fighting** 🏹
- **Robb is riding** 🐎
- **Arya is playing** ⚔️

Here, *Jon, Robb, and Arya* are **subjects** performing actions. In OOP, the **subject (object) controls the action (method)**.

🔹 **Class:** A **blueprint** or **template** for creating objects.

🔹 **Object:** A **real entity** created from a class.

> 📌 Example: A Car class defines general properties (color, wheels) and behavior (start, stop). An actual car object has specific values for these.
> 

```java
class Student {
    int age;
    String name;
    int batch;
    int marks;
}

Student s1 = new Student(); // Object created

```

📝 **Objects are stored in Heap, and their references are in Stack.**

---

## **2️⃣ Procedural vs. OOP Programming**

### ❌ **Problem with Procedural Programming**

- **Functions operate on global data**, making code **error-prone** and **inconsistent**.
- Example: `getInterest()` function is used in multiple files. If interest rate changes, we must update all occurrences manually.

### ✅ **OOP Solves This with Classes**

We encapsulate related data and behavior inside **objects**.

```java
class Student {
    int age;
    String name;
    int batch;
    int marks;

    void printDetails() {
        System.out.println(age + ", " + name + ", " + batch + ", " + marks);
    }
}

Student s = new Student();
s.printDetails(); // Now, subject (s) controls the action

```

🔹 **Encapsulation ensures that only the class controls its data.**
🔹 **Methods should not alter data unintentionally.**

---

## **3️⃣ Access Modifiers**

| Modifier | Scope | Access Outside Class? |
| --- | --- | --- |
| **private** | Same class only | ❌ No |
| **protected** | Same package + subclasses | ⚠️ Only if subclass |
| **public** | Everywhere | ✅ Yes |
| **default** *(no modifier)* | Same package only | ❌ No |

🔹 **Example:**

```java
class A {
    private int privateVar = 10;
    protected int protectedVar = 20;
    public int publicVar = 30;
    int defaultVar = 40; // Default modifier
}

```

---

## **4️⃣ Constructor**

- A **special method** used to initialize objects.
- **No return type** and **same name as class**.

```java
class Student {
    String name;
    int age;

    // Constructor
    Student(String name, int age) {
        this.name = name;
        this.age = age;
    }
}

Student s1 = new Student("Arya", 18);

```

❗ If you define a **parameterized constructor**, the **default constructor is removed automatically**.

---

## **5️⃣ `this` Keyword**

- Refers to **current object instance**.
- Helps differentiate between **instance variables and parameters**.

```java
class Student {
    String name;

    void setName(String name) {
        this.name = name;  // Refers to instance variable
    }
}

```

📝 Without `this`, `name = name;` would update only the local variable, not the object's property.

---

## **6️⃣ Abstraction vs. Encapsulation**

| Concept | Meaning | Example |
| --- | --- | --- |
| **Abstraction** | Hiding internal details, showing only necessary info | Car pedals (we use them but don’t see the mechanics) |
| **Encapsulation** | Binding data with methods to protect it | Private variables with `getters/setters` |

🔹 **Example of Encapsulation:**

```java
class Student {
    private int marks;

    // Setter
    void setMarks(int marks) {
        this.marks = marks;
    }
    // Getter
    int getMarks() {
        return marks;
    }
}

```

✔️ **Data is controlled with methods, preventing direct modification.**

---

##