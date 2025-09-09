# Generic

---

### ✅ Problem: Storing Coordinates of Places (Latitude & Longitude)

We want to store coordinates where each coordinate consists of a **latitude and longitude**.

**Approach 1:**

```java
ArrayList<ArrayList<Integer>>

```

- We can use a 2D ArrayList where the **outer list** stores all the coordinates.
- The **inner list** contains two values — **latitude** and **longitude**.

---

**Approach 2: Using a Custom Class**

We can create a custom class like this:

```java
class Pair {
    Double lat;
    Double lon;
}

```

- Each object of `Pair` will store the latitude and longitude of a place.
- You can now store coordinates like this:

```java
ArrayList<Pair> coordinatesList;

```

This `ArrayList<Pair>` will store references to `Pair` objects.

---

### ✅ Storing Country and Population

Let’s say we want to store:

- America → X
- Britain → Y

We can again use either a 2D ArrayList or a custom class:

```java
class Pair2 {
    String country;
    int population;
}

```

```java
List<Pair2> countryPopulationList;

```

> Why we used List<Pair2> instead of ArrayList<Pair2>?
Because ArrayList is a child class of List. We can use the parent class List to refer to a child class ArrayList.
> 

---

### 🔁 Observation: Both Use Cases Use Pairs but with Different Data Types

- Coordinates → (Double, Double)
- Country-Population → (String, Integer)

Should we create different classes like `Pair1`, `Pair2`, `Pair3` each time? ❌

Instead, we can generalize this using **Object references**:

```java
class Pair {
    Object first;
    Object second;

    Pair(Object f, Object s) {
        this.first = f;
        this.second = s;
    }
}

```

Java classes like `Double`, `String`, `Integer` all **inherit from `Object`**, so this works:

```java
Pair p1 = new Pair(1.2, 3.4);      // Double, Double
Pair p2 = new Pair("India", 10000); // String, Integer

```

Because `Double`, `String`, `Integer` are subclasses of `Object`, and **parent class can refer to child objects**.

BUT — ❗ There's a major **problem** here:

- In `p2`, I can do: `p2.first = 5.7` → this is not intended (should be a String).
- In `p1`, I can do: `p1.first = "India"` → again, not intended (should be a Double).

This may lead to **runtime errors**, and these bugs are **hard to track**.

---

### ✅ Solution: Use Generics

You’ve seen syntax like this:

```java
ArrayList<Integer>

```

What you write inside `< >` is called a **generic type parameter**.

Let’s make our `Pair` class generic:

```java
class Pair<V, S> {
    V first;
    S second;

    Pair(V f, S s) {
        this.first = f;
        this.second = s;
    }

    public void setFirst(V val) {
        this.first = val;
    }

    public V getFirst() {
        return this.first;
    }
    // Same for second
}

```

Now, when you create objects:

```java
Pair<Integer, Integer> p1 = new Pair<>(1, 2);
Pair<String, Integer> p2 = new Pair<>("India", 1000);

```

- In `p1`, `V = Integer`, `S = Integer`
- In `p2`, `V = String`, `S = Integer`

So now:

```java
p1.setFirst("India");   // ❌ Compile-time error
p2.setFirst(10.9);       // ❌ Compile-time error

```

✅ We solved two problems:

1. No need to write multiple classes.
2. Compile-time safety.

---

### 🕰️ Before Java 5 — No Generics

Previously, people used:

```java
class Pair {
    Object first;
    Object second;
}

```

They may have applied manual checks.

Java 5 introduced **generics** but didn’t **remove the old approach** to keep **backward compatibility**.

---

### 💡 Raw Type Example

```java
HashMap<Integer, Integer> hm = new HashMap<>();   // ✅ Recommended
HashMap hm = new HashMap();                       // ❌ Raw type (not recommended)

```

---

### 🔁 Generics and Inheritance

```java
class Animal {}
class Dog extends Animal {}

Animal a = new Dog(); // ✅ Parent can refer to child

```

Suppose you have:

```java
public void doSomething(List<Animal> animalList)

```

Now:

```java
List<Animal> l1;  // ✅ will work
List<Dog> l2;     // ❌ won't work

```

Even though `Dog` extends `Animal`, **`List<Dog>` is not a subtype of `List<Animal>`**. 

That’s like saying:

> Function expects List<Integer> and you pass List<String> — ❌ Error
> 

---

### ✅ Solution: Generic Methods

```java
public <T> void doSomething(List<T> list) {
    // works with List<Animal>, List<Dog>, etc.
}

```

Now you can pass:

```java
List<Animal> l1;
List<Dog> l2;

```

Both will work. If you call `doSomething(l2)`, the compiler sees it like:

```java
public void doSomething(List<Dog>)

```

---

### 🔼 Upper Bound: Restrict Types

```java
public void doSomething(List<? extends Animal>) {
    // Only accepts Animal or its child classes
}

```

This fancy way restricts the type and is more readable.
⚠️ But note: **You can't add elements** to the list (except `null`). You can only **read/remove**.

---

### 📌 Type Erasure in Java

At compile time, generic info is used for safety.
But at runtime, JVM uses **type erasure** — meaning:

```java
Pair<V, S> → becomes → Pair<Object, Object>

```

So your code:

```java
class Pair<V, S> {
    V first;
    S second;
}

```

Gets translated to:

```java
class Pair {
    Object first;
    Object second;
}

```

The compiler checks types only during compilation. At runtime, everything is just `Object`.

---

### ✅ Summary

- Use custom classes when structure is required.
- Use generics to make your class flexible and type-safe.
- Java generics provide compile-time checks and prevent runtime errors.
- Understand inheritance + generics together.
- Learn bounds (`extends`, `super`) to restrict or allow specific type hierarchies.
- JVM erases generic type info during runtime — this is called **type erasure**.

---