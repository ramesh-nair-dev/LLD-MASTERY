# Singelton Pattern

---

### 🧠 Singleton Design Pattern

---

Design patterns — these are nothing but solutions to common problems that arise on a daily basis.

Let’s discuss today’s topic: the **Singleton Design Pattern**.

So what is the Singleton Design Pattern?

It is nothing but a design where **we want only one object to be created** — i.e., we should allow only **one instance of a class** to exist.

---

### ✅ For example:

```java
class ABC {
}

```

```java
ABC a1 = new ABC();  // It will create one object at, say, 10K
ABC a2 = new ABC();  // It will create another object at 20K

```

Our requirement: we should allow the client to **create only one instance** of the class.

---

### 💡 Where is Singleton used?

For example, in our code, we might be using the **database connection** at many places.

Instead of creating a **new DB connection** at multiple places for the same task,

we can **create one object of the DB connection** and pass it where needed.

---

### ❌ Let's try a naive solution:

```java
class DBCom {
}

```

```java
DBCom d1 = new DBCom();
DBCom d2 = new DBCom();

```

If I have written this class in a conventional way, **can anybody stop me from creating multiple instances?**

**No!** So this solution **does not work**.

---

### ✅ Version 2 — Make Constructor Private

```java
class DBCom {
    private DBCom() {
    }
    // What we did here is made our constructor private.
    // This constructor can’t be called from outside the class.
    // It can only be used *inside* the class.
}

```

Now try:

```java
DBCom d1 = new DBCom(); // ❌ Error — private constructor
DBCom d2 = new DBCom(); // ❌ Same error

```

In this version, **I can't even create one instance** because:

```java
DBCom d1 = new DBCom();

```

We are trying to call a **private constructor outside the class**, which is not allowed.

---

### ✅ Version 3 — Add Static Method

```java
class DBCom {
    private DBCom() {
    }

    static DBCom getInstance() {
        return new DBCom();
    }
}

```

What we did here:

- Created a **private constructor** — ensures client can’t instantiate the object directly.
- Added a **static method `getInstance()`** — this can be accessed at class . it can directly be accessed without creating the instance of the class

Try:

```java
DBCom d1 = DBCom.getInstance();
DBCom d2 = DBCom.getInstance();

```

Does this version solve our problem?

**No!** Because what’s still happening:

```java
DBCom d1 = DBCom.getInstance(); // object at 10K
DBCom d2 = DBCom.getInstance(); // object at 20K

```

Even though we use a static method, **it still creates a new object every time**.

---

### ✅ Version 4 — Lazy Initialization

```java
class DBCom {
    private static DBCom dbcomInstance = null;

    private DBCom() {
    }

    static DBCom getInstance() {
        if (dbcomInstance == null) {
            dbcomInstance = new DBCom();
        }
        return dbcomInstance;
    }
}

```

Why did we make `dbcomInstance` static?

Because:

- Static variables are **class level**
- Static method `getInstance()` cannot access instance variables

Now try:

```java
DBCom d1 = DBCom.getInstance();
DBCom d2 = DBCom.getInstance();

```

Let’s see how the first line executes:

- Initially, `dbcomInstance` is `null`
- So the `if` clause runs and creates an object at 10K
- It sets `dbcomInstance` to that object and returns it

Now the second line:

- `dbcomInstance` is not `null` anymore (already set by `d1`)
- So it skips creation and returns the same object (at 10K)

✅ So both `d1` and `d2` point to the **same object at 10K**

---

### 🐢 This is called **Lazy Initialization**

Why "lazy"?

Because we **only start the creation process when it's actually needed**.

---

### ✅ Version 5 — Eager Initialization

```java
class DBCom {
    private static DBCom dbcomInstance = new DBCom();

    private DBCom() {
    }

    static DBCom getInstance() {
        return dbcomInstance;
    }
}

```

What we did here:

- Directly initialized the static variable with a new object.
- Suppose the object is at 10K.

**How do static variables behave?**

- They are loaded **when the class is loaded by the JVM**
- And since they're class-level, it's **shared across the class**

Now:

```java
DBCom d1 = DBCom.getInstance();
DBCom d2 = DBCom.getInstance();

```

Both calls just **return the same static variable** pointing to the object at 10K.

✅ This is called **Eager Initialization**

---

### 🍔 Real-life Analogy: Eager vs Lazy Initialization

There are two burger shops:

- **Shop 1** opens at 6 AM. It estimates that 100 burgers will be sold and **prepares all 100** before any customer shows up.
    - Problem: too much effort before actually running the shop.
    - This is **Eager Initialization**
- **Shop 2** opens at 9 AM, sets up, and **prepares burgers only when orders come**.
    - This is **Lazy Initialization**

> Lazy is more efficient if the resource may not be needed.
> 
> 
> But if time is not a concern, Eager is fine.
> 

---

### ⚠️ Problem: Is Lazy Initialization Thread-Safe?

Let’s take this example:

```java
class DBCom {
    private static DBCom dbcomInstance = null;

    private DBCom() {
    }

    static DBCom getInstance() {
        if (dbcomInstance == null) {
            dbcomInstance = new DBCom();
        }
        return dbcomInstance;
    }
}

```

Now let’s say **5 threads** come and all enter `getInstance()`:

- All 5 threads read `dbcomInstance == null`
- So each creates a new object:
    - Thread 1 → object at 10K
    - Thread 2 → object at 20K
    - ...
    - Thread 5 → object at 60K

**Result**: ❌ Not expected behavior — **multiple objects created**

Let’s say after that, all 5 threads finish and now `dbcomInstance` is set to object at 60K.

Now, new threads come — they’ll read the non-null instance and return the 60K object.

But we have **already wasted memory** by creating 5 useless objects earlier.

---

### 🧠 How to fix? Use synchronization smartly.

Let’s try this:

```java
class DBCom {
    private static DBCom dbcomInstance = null;

    private DBCom() {
    }

    static DBCom getInstance() {
        synchronized (DBCom.class) {
            if (dbcomInstance == null) {
                dbcomInstance = new DBCom();
            }
        }
        return dbcomInstance;
    }
}

```

**Problem?**

Yes — now **every thread has to wait**, even when the instance is already created.

This **kills multithreading performance**.

---

### ✅ Smart Version — Double-Checked Locking

```java
class DBCom {
    private static volatile DBCom dbcomInstance = null;

    private DBCom() {
    }

    static DBCom getInstance() {
        if (dbcomInstance == null) {
            synchronized (DBCom.class) {
                if (dbcomInstance == null) {
                    dbcomInstance = new DBCom();
                }
            }
        }
        return dbcomInstance;
    }
}

```

Let’s go back to our earlier condition where 5 threads arrive:

- All 5 read: `dbcomInstance == null`
- All enter `if` condition

Now:

- **Thread 1** takes the lock → checks again → `null` → creates object at 10K → sets `dbcomInstance` → releases lock
- **Thread 2** takes the lock → checks again → now `dbcomInstance != null` → skips creation → returns existing object
- Same for Thread 3, 4, 5

Now let’s say **10 more threads** come:

- First `if` condition fails (`dbcomInstance != null`)
- All 10 return the same instance — no locking needed

✅ We ensured only one object is created

✅ We preserved multithreading performance

✅ We avoided memory waste

---

###