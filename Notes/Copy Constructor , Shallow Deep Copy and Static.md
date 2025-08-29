# Copy Constructor , Shallow Deep Copy and Static

## 📌 **Copy Constructor**

### 🔹 **Understanding Object Memory in Heap**

- Objects in Java are stored in **heap memory**.
- When we assign an object reference to another, they point to the **same memory location**.

### 🔹 **Shallow Copy** (Same Memory Reference)

```java
Student s1 = new Student();  // Object at 10k location
Student s2 = s1;             // s2 also points to 10k location

```

✅ Both `s1` and `s2` refer to the **same object** in heap memory.
❌ Any changes made to `s2` will reflect in `s1` as well.

### 🔹 **Deep Copy** (Separate Memory Allocation)

```java
Student s1 = new Student();  // Object at 10k location
Student s2 = new Student();  // Object at 11k location

s2.name = s1.name;
s2.age = s1.age;
s2.batch = s1.batch;

```

✅ Now, `s1` and `s2` are **separate objects** with the same initial data.
❌ Changes in `s2` will **not** affect `s1`.

### 🔹 **Copy Constructor** (Automating Deep Copy)

```java
class Student {
    String name;
    int age;
    int batch;

    // Copy Constructor
    Student(Student other) {
        this.name = other.name;
        this.age = other.age;
        this.batch = other.batch;
    }
}

Student s1 = new Student();
Student s2 = new Student(s1);  // s2 is a deep copy of s1

```

✅ `s2` is now a **new object** with the same properties as `s1`, but independent.

---

## 📌 **Static Keyword**

### 🔹 **What is a Static Variable?**

- A **class-level** variable that is shared across all objects.
- Stored in **Metaspace**, not in Heap.
- Created when JVM loads the class **before** any object is created.

### 🔹 **Example of Static Variable**

```java
class Student {
    static int studentCount = 0;  // Static variable
    String name;
}

Student s1 = new Student();
Student s2 = new Student();
System.out.println(Student.studentCount);  // Access using ClassName

```

✅ `studentCount` is shared among all objects.

### 🔹 **Static Block** (Executed Before Object Creation)

```java
class Student {
    static {
        System.out.println("Class Loaded!" );
    }
}

```

✅ Runs **once** when the class is first loaded by the JVM.

### 🔹 **Static Function**

- Belongs to the **class, not objects**.
- Cannot access **instance (non-static) variables**.

```java
class Student {
    static int count = 0;
    String name;

    static void printCount() {
        System.out.println(count);  // ✅ Allowed
        // System.out.println(name);  // ❌ Not allowed (name is instance variable)
    }
}

```

✅ Can be accessed using `ClassName.methodName()`

---

## 📌 **Quick Comparison Table**

| Feature | Shallow Copy | Deep Copy |
| --- | --- | --- |
| Memory | Same object reference | Separate object in memory |
| Changes | Affects both objects | Independent copies |
| Usage | Faster but risky | Safer but requires extra steps |

---