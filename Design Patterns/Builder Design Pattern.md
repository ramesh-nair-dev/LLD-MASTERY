# Builder Design Pattern

### 

---

**Introduction:**

Today we are discussing the **Builder Pattern**. Let’s begin with an example.

Suppose we have a `Student` class with **30 attributes**, out of which **3 are mandatory** to create a valid student object. The remaining attributes are optional. So, how do we design this in an optimal way?

---

### 🚧 Problem 1: Using Constructors (Constructor Overloading)

We can try to solve this using constructor overloading:

```java
Student(String name, String batch) // mandatory fields only
Student(String name, String batch, double psp) // with one optional field
Student(String name, String batch, double psp, int gradYear) // and so on...

```

But this approach quickly leads to:

1. **Constructor Bloating** – You’ll need multiple permutations and combinations to cover every possible scenario of optional fields.
2. **Maintainability Issues** – Too many constructors make code harder to read and maintain.
3. **Parameter Order Sensitivity** – If there are many parameters like `(String name, String batch, double psp, int gradYear)`, the client can easily mess up the order: e.g., calling it like `(name, batch, gradYear, psp)`.
4. **Signature Conflict** – You can't have two constructors with the same method signature even if their logical purpose is different. For example:
    
    ```java
    Student(String bookName, String authorName)
    Student(String bookName, String publisherName)
    
    ```
    
    This will cause a compilation error.
    

---

### 🛠️ Problem 2: Using Setters (JavaBeans Style)

In this approach:

```java
Student student = new Student();
student.setName("John");
student.setBatch("March 2024");
student.setGradYear(2028);

```

### Problems with Setters:

- The object gets created before validation. If `gradYear > 2024` is invalid, we can’t prevent this at object creation time *(for our case let’s say we want only student with gradyear ≤ 2024)*
- You might think of making `gradYear` final to prevent changes, but then it can’t be set via setter.
- We want immutability after object creation, but setters inherently allow modifications at any time.

So, while this approach provides flexibility, it compromises **validation, encapsulation, and immutability**.

---

### 🧩 Problem 3: Pass Key-Value Pairs via HashMap

Here’s the idea:

```java
HashMap<String, Object> studentData = new HashMap<>();
studentData.put("name", "John");
studentData.put("psp", 90.5);
Student student = new Student(studentData);

```

- You pass a key-value map into the constructor.
- This gives flexibility, but it sacrifices **type safety**.
- At compile time, you won’t catch errors like:

```java
studentData.get("name").add(10); // logically wrong, compiles fine

```

This violates **robust type-checking** and introduces runtime risk.

---

### ✅ Solution: Use the Builder Pattern (Version 0)

Let’s create an intermediate `Builder` class that holds the same attributes as the `Student` class.

```java
class StudentBuilder {
    String name;
    int age;
    double psp;
    String batch;
    int gradYear;

    // setters for each attribute
}

class Student {
    final String name;
    final int age;
    final double psp;
    final String batch;
    final int gradYear;

    Student(StudentBuilder builder) {
        if (builder.gradYear > 2024) {
            throw new IllegalArgumentException("GradYear must be <= 2024");
        }

        this.name = builder.name;
        this.age = builder.age;
        this.psp = builder.psp;
        this.batch = builder.batch;
        this.gradYear = builder.gradYear;
    }
}

```

### 🔄 Flow:

1. Build all attributes in the `StudentBuilder`.
2. Validate in the `Student` constructor.
3. Once a `Student` object is created, there are no setters – making it immutable.

---

### 📦 Version 1: Improve Builder Integration

We add a **static method** in `Student` class:

```java
public static StudentBuilder getBuilder() {
    return new StudentBuilder();
}

```

This way, we don’t need to instantiate `Student` to access the builder. This avoids the deadlock of needing the outer class to create the builder.

---

### ✅ Why Make `StudentBuilder` a Static Nested Class?

In Java, a **non-static inner class** requires an instance of the outer class. But our goal is to use the builder before the `Student` object exists — that’s the whole point of the Builder Pattern.

So, we make `StudentBuilder` a **static nested class**, allowing this usage:

```java
Student s = Student.getBuilder()
    .setAge(25)
    .setName("John")
    .setBatch("May 2024")
    .setGradYear(2024)
    .build();

```

This works **without needing a pre-existing `Student` object**, solving the "deadlock" of needing the outer class to build itself.

---

### 🧪 Bonus: Fluent Setters

Instead of writing setters like:

```java
public void setAge(int age) {
    this.age = age;
}

```

We use **fluent setters**:

```java
public StudentBuilder setAge(int age) {
    this.age = age;
    return this;
}

```

This allows chaining like streams:

```java
Student st = Student.getBuilder()
    .setAge(25)
    .setBatch("Nov Advanced")
    .setGradYear(2024)
    .build();

```

---

### 📐 Final Implementation Structure (With Explanations)

```java
public class Student {
    // Immutable fields (no setters)
    final int age;
    final String name;
    final String batch;
    final int gradYear;

    // Optional fields
    String course;
    int psp;

    // Private constructor to enforce object creation only via builder
    private Student(StudentBuilder builder) {
        this.age = builder.age;
        this.name = builder.name;
        this.batch = builder.batch;
        this.gradYear = builder.gradYear;
        this.course = builder.course != null ? builder.course : "Academy"; // default value
    }

    // Static method to get builder instance
    public static StudentBuilder getBuilder() {
        return new StudentBuilder();
    }

    // Static nested Builder class
    public static class StudentBuilder {
        // Same fields as Student
        int age;
        String name;
        String batch;
        int gradYear;
        String course;

        // Fluent setter methods
        public StudentBuilder setAge(int age) {
            this.age = age;
            return this;
        }

        public StudentBuilder setName(String name) {
            this.name = name;
            return this;
        }

        public StudentBuilder setBatch(String batch) {
            this.batch = batch;
            return this;
        }

        public StudentBuilder setGradYear(int gradYear) {
            this.gradYear = gradYear;
            return this;
        }

        public StudentBuilder setCourse(String course) {
            this.course = course;
            return this;
        }

        // Final step to build Student object after validation
        public Student build() {
         if (builder.age < 10) {
            throw new IllegalArgumentException("Age must be at least 10");
        }
        if (builder.gradYear > 2024) {
            throw new IllegalArgumentException("GradYear must be <= 2024");
        }
            return new Student(this);
        }
    }
}

```

---

### 📦 Client Code Example

```java
Student student = Student.getBuilder()
    .setAge(22)
    .setName("Alice")
    .setBatch("Jan 2024")
    .setGradYear(2024)
    .setCourse("DSA")
    .build();

```

---

### 🔚 Summary

- Builder Pattern solves constructor bloating and order issues.
- Encourages immutability and compile-time safety.
- Great for objects with many optional attributes.
- Fluent interface makes client code readable and robust.

✅ **Validation** is done before the object is created.

✅ **Only one final object** is created. No setters are exposed.

✅ **Clean, flexible, and safe.**