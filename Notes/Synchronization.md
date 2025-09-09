# Synchronization

### 🧠 Synchronization Issue in Multithreading

---

### 🔍 Problem Overview

- **Synchronization issue** arises when multiple threads try to access and modify the **same shared data** concurrently.

---

### 🔥 Real-Life Scenario:

Let's assume a shared variable:

```java
val = 0

```

We have two types of code being run by threads:

### 🔼 Code A (5 threads running this):

```java
print();
val += 1;
print();

```

### 🔽 Code B (5 threads running this):

```java
print();
val -= 1;
print();

```

Both are working on the same shared variable `val`, and both types of threads are executing **in parallel**.

---

### 🛠️ What is the Critical Section?

- The lines `val += 1` and `val -= 1` are **critical sections**.
- A critical section is a part of code where the shared resource (`val`) is being **modified**.

---

### ⚠️ Problem in Multithreading:

Suppose the system runs all threads **partially** (due to context switching):

### Context Switch Walkthrough:

- `Thread 1` executes: `print()` → stores val = 0, then **context switch**.
- `Thread 2` executes: same as above (still thinks val = 0).
- Similarly, Threads 3, 4, and 5 all read val = 0.

Now when we resume:

- `Thread 1`: `val = val + 1 → 1`
- `Thread 2`: still thinks val = 0 → `val = val + 1 → 1`  so thread 1 val = 1 and thread 2 val = 1 both are 1 but actually thread 2 should be 2
- ... and so on, final `val` ends up being **less than expected or inconsistent**.

---

### 🤔 Why Does This Happen?

1. **Critical Section Conflict** – multiple threads work on the same resource.
2. **Race Condition** – threads race to access/modify shared data.
3. **Preemptive Multitasking** – OS switches threads mid-execution.

---

### 🏛️ Analogy: Museum & Statue Room

**Goal:** Only one person should view the statue at a time.

- There are 4 doors, people stand in queue at each.
- You install **separate locks & keys** at each door → ❌ FAIL. All can enter at the same time.
- ✅ SOLUTION: One **shared lock and one key** for all doors.
    - Person from Door 1 takes the key, locks other doors, enters.
    - No one else can enter until key is returned.

> 💡 Just like only one thread should be able to enter the critical section at a time using a lock.
> 

---

### 🔐 Solution with Lock:

```java
print();
lock();
val = val + 1;
unlock();
print();

```

If `Thread 1` locks, no other thread can proceed until it unlocks. Even with context switch, `Thread 2` will wait for the lock.

---

### 🧪 Code Without Lock (Race Condition)

```java
class Adder implements Runnable {
    private Value v;
    Adder(Value v) { this.v = v; }

    public void run() {
        for (int i = 1; i <= 100; i++) {
            this.v.value = this.v.value + 1;
        }
    }
}

class Subtractor implements Runnable {
    private Value v;
    Subtractor(Value v) { this.v = v; }

    public void run() {
        for (int i = 1; i <= 100; i++) {
            this.v.value = this.v.value - 1;
        }
    }
}

class Value {
    public int value;
    Value(int v) { this.value = v; }
}

class Client {
    Value value = new Value(0);
    Adder adder = new Adder(value);
    Subtractor subtractor = new Subtractor(value);
    Thread t1 = new Thread(adder);
    Thread t2 = new Thread(subtractor);

    t1.start();
    t2.start();
}

```

### ❌ Expected Result:

Final value = 0 (because 100 additions and 100 subtractions)

### ❌ Actual Result:

Value is **not guaranteed** to be 0 due to **context switching** and race conditions.

### Why?

Operations like:

```java
this.v.value = this.v.value + 1;

```

are **not atomic**. They involve:

- Read `v.value` into a temporary variable
- Increment temp
- Write temp back to `v.value`

Between these steps, a context switch can mess things up.

---

### ✅ Solving Using Lock (Manual)

```java
public class Adder implements Runnable {
    private Count count;
    private Lock lock;

    public Adder(Count count, Lock lock) {
        this.count = count;
        this.lock = lock;
    }

    public void run() {
        for (int i = 1; i <= 100; i++) {
            lock.lock();
	            count.value += i; // we add lock there because this is the critical section						lock.unlock();
        }
    }
}

```

- If thread reaches `lock()`, it gets access.
- If a context switch happens, the next thread **can't enter** until the lock is released.

---

### 🔐 Java Built-In: Synchronized Block

```java
class Adder implements Runnable {
    private Value v;
    Adder(Value v) { this.v = v; }

    public void run() {
        for (int i = 1; i <= 100; i++) {
            synchronized(this.v) {
                this.v.value = this.v.value + 1;
            }
        }
    }
}

```

- We synchronize on `this.v` because it's the **shared memory**.
- Only one thread can lock this object at a time.

---

### 🔁 Synchronized Method Example:

```java
class Cal {
    synchronized void add() {
        // critical code
    }

    synchronized void mul() {
        // critical code
    }
}

Thread t1 = new Thread(() -> cal.add());
Thread t2 = new Thread(() -> cal.mul());

```

- Both `add()` and `mul()` are synchronized.
- They will lock the **object** (`cal`) before entering.
- Only one method will execute at a time.

---

### 🧠 Final Takeaways:

- Always protect shared resources with locks.
- Understand how **context switching** causes unpredictable behavior.
- Use either manual `Lock` or Java’s `synchronized` keyword.
- Think like a museum – only one person (thread) should see the statue (modify shared data) at a time.

---