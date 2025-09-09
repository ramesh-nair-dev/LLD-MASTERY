# Prototype and Registry Pattern

---

### 🧠 Problem Statement

We want to **create copies of objects** efficiently. In games like BGMI, a single match has 100 players—60 real and 40 bots. Each bot is an object with attributes like:

- Name
- Clothes
- Appearance
- Gender

Creating 40 bot objects from scratch (with all validations, rules, and object setup) is computationally expensive. Instead, we need a way to **clone existing, validated objects** with slight variations (like different name or appearance).

---

### 🚫 Problem with Manual Field Copying

```java
Student st1 = new Student();
Student st2 = new Student();
st2.name = st1.name;
st2.age = st1.age;
st2.psp = st1.psp;

```

### What’s happening:

- Two objects are created: `st1` at (say) memory address 5K, and `st2` at 10K.
- We manually copy fields from `st1` to `st2`.

### Why this is bad:

- **Boilerplate Code:** Tedious and repetitive.
- **Not Reusable:** You’d have to do this for every class.
- **Encapsulation Broken:** If fields are `private`, they’re not accessible.
- **Getters/Setters Limitations:** Not all fields may have them. Risk of incomplete or inconsistent copy.

---

### 💡 Idea: Copy Constructor

```java
class Student {
    String name;
    int age;
    int psp;

    Student(Student other) {
        this.name = other.name;
        this.age = other.age;
        this.psp = other.psp;
    }
}

Student s1 = new Student();
Student s2 = new Student(s1); // Deep copy using constructor

```

### What’s happening behind the scenes:

- `s1` is a fully constructed object.
- `s2` is created using `s1`. The constructor copies each field.
- New memory is allocated for `s2`. `s1` and `s2` are separate objects.

### Limitations:

Let’s say we have a subclass:

```java
class IntelligentStudent extends Student {
    int iq;
}

```

If we use:

```java
doSomething(Student s) {
    Student copy = new Student(s); // always calls Student copy constructor
}

```

If we pass an `IntelligentStudent` object, the copy will **lose the IQ field** because the constructor only knows about `Student` copy constructor , not the subclass copy constructor.

---

### ✅ Solution: `copy()` Method with Polymorphism

We solve this by allowing each class to define how it should be copied.

```java
class Student {
    String name;
    int age;
    int psp;

    public Student copy() {
        Student s2 = new Student();
        s2.name = this.name;
        s2.age = this.age;
        s2.psp = this.psp;
        return s2;
    }
}

class IntelligentStudent extends Student {
    int iq;

    public Student copy() {
        IntelligentStudent si = new IntelligentStudent();
        si.name = this.name;
        si.age = this.age;
        si.psp = this.psp;
        si.iq = this.iq;
        return si;
    }
}

// In client we do this

Student s = new Student()
Student si = new IntelligentStudent()

doSomething(Student s) {
    Student copy = s.copy();
```

### Behind the scenes:

- `s.copy()` calls the right version depending on the actual object (runtime polymorphism).
- If the object is `IntelligentStudent`, its own `copy()` is used.
- Fields are cloned manually inside the appropriate method.

---

### ⚠️ Problem: `private` Fields Can’t Be Accessed

If fields are private:

```java
class Student {
    private int age;
}

```

You can’t do:

```java
si.age = this.age;

```

To fix this, we use **copy constructors** with access within the class:

---

### 🛠️ Final Deep Solution Using Copy Constructors (Cleanest Way)

```java
class Student {
    String name;
    private int age;
    int psp;

    Student() {}

    Student(Student other) {
        this.name = other.name;
        this.age = other.age;
        this.psp = other.psp;
    }

    public Student copy() {
        return new Student(this);
    }
}

class IntelligentStudent extends Student {
    int iq;

    IntelligentStudent() {}

    IntelligentStudent(IntelligentStudent other) {
        super(other);
        this.iq = other.iq;
    }

    public Student copy() {
        return new IntelligentStudent(this);
    }
}

```

### Behind the scenes:

- `super(other)` allows copying of private fields.
- `copy()` creates an exact replica of the current object.
- Memory for new object is allocated separately.
- Original and copy are independent.

---

### 🧬 Cloneable Interface (Alternative Approach)

Java provides a `Cloneable` interface:

```java
class Student implements Cloneable {
    String name;
    int age;

    public Object clone() throws CloneNotSupportedException {
        return super.clone();
    }
}

```

### Drawbacks:

- Shallow copy by default (if object contains references, they point to same sub-objects).
- Doesn’t play well with inheritance.
- Must handle `CloneNotSupportedException`.
- Often discouraged in favor of manually controlled copying via constructors or methods.

---

### 🧾 Registry Pattern – Managing Prototypes

We don’t want to hardcode or manually create prototypes. Let’s store them in a central place:

```java
class StudentRegistry {
    Map<String, Student> registry = new HashMap<>();

    void register(String key, Student prototype) {
        registry.put(key, prototype);
    }

    Student get(String key) {
        return registry.get(key).copy();
    }
}

```

### How it works:

- Pre-create prototypes (e.g., `basicBot`, `intelligentBot`) and store them.
- When a new object is needed, just `copy()` the prototype.
- Avoids re-creating and re-validating from scratch.

---

### ✅ Summary

- **Manual copying** breaks encapsulation and is not scalable.
- **Copy constructors** are great when used carefully.
- **Polymorphic `copy()` methods** handle subclassing elegantly.
- **`Cloneable`** exists but isn’t preferred.
- **Registry pattern** allows efficient prototype storage and reuse.

---

