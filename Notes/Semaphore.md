# Semaphore

---

# 🧵 Producer and Consumer problem

---

### 👨‍🏭 Producer produces items in the store

### 👨‍🍳 Consumer consumes items from the store

Let’s say we have a **store** that has a **maximum capacity of 5 items**.

- 🧺 At max, **Producer** will produce 5 items.
- 🍽️ At max, **Consumer** will consume 5 items.

---

## ✅ Step 1: Create the Store Class

```java
class Store {
    int maxSize; // We need to define an upper limit on the store
    List<Object> items; // ArrayList to store the items

    Store(int maxSize) {
        this.maxSize = maxSize;
        this.items = new ArrayList<>();
    }

    public int maxSize() {
        return this.maxSize;
    }

    public List<Object> getItem() {
        return this.items; // Returns the list of stored items
    }

    public void addItem() {
        System.out.println("Adding item");
        this.items.add(new Object()); // Adding a dummy object for understanding
    }

    public void removeItem() {
        System.out.println("Removing item"); 
        this.items.remove(this.items.size() - 1);
    }
}

```

---

## ✅ Step 2: Producer Class

```java
class Producer implements Runnable {
    Store st;

    Producer(Store st) {
        this.st = st;
    }

    public void run() {
        while (true) {
            if (st.getItem().size() < st.maxSize) { // Infinite loop that adds until max size
                st.addItem();
            }
        }
    }
}

```

🧵 This code runs on a **separate thread**.

---

## ✅ Step 3: Consumer Class

```java
class Consumer implements Runnable {
    Store st;

    Consumer(Store st) {
        this.st = st;
    }

    public void run() {
        while (true) {
            if (st.getItem().size() > 0) {
                st.removeItem();
            }
        }
    }
}

```

---

## ✅ Step 4: Client Code (Thread Management)

```java
public class Client {
    public static void main(String[] args) {
        ExecutorService es = Executors.newCachedThreadPool();
        Store store = new Store(5);

        for (int i = 1; i <= 8; i++) {
            es.execute(new Producer(store));
        }

        for (int i = 1; i <= 20; i++) {
            es.execute(new Consumer(store));
        }
    }
}

```

---

### ❌ Problem: Code **won’t work correctly** due to **Race Condition**

### ❗ Let’s understand it step-by-step:

Let’s say the initial `size = 3`

- **Thread 1** sees size 3 → context switch
- **Thread 2** sees size 3 → context switch
- **Thread 3** sees size 3 → context switch
- **Thread 4** sees size 3 → context switch
- **Thread 5** sees size 3 → context switch

Now back to execution:

- Thread 1 adds → size becomes 4
- Thread 2 adds → actual size has become 4 but thread has remembers the size as 3 then it will add item this is wrong in the truth it started is now false

Why? All threads read the *same stale size* value before any had a chance to update it. This is a classic **race condition** due to **no synchronization** on shared data access.

---

## ❌ Now Consider Consumer Side:

Suppose current size is `1`

- Thread 1 sees size > 0 → context switch
- Thread 2 sees size > 0 → context switch
- Thread 3 sees size > 0 → context switch

Back to execution:

- Thread 1 removes → size =  0
- Thread 2 removes → ❌ list is now empty it will lead to array index out of bound error because thread 1 saw that initial size was 1 so it removed an item from the List , now thread 2 also saw that initial size was 1 actually it not so when it tries to remove this will lead to error

Again, threads working on *stale* size values. ❗

---

# 🛡️ Solution: Synchronize the **Critical Section**

```java
while(true) {
    synchronized(store) {
        if (st.getItem().size() < st.maxSize) {
            st.addItem();
        }
    }
}

```

```java
while(true) {
    synchronized(store) {
        if (st.getItem().size() > 0) {
            st.removeItem();
        }
    }
}

```

But this is still **not optimal** ⛔

Let’s say maxSize = 5 and store has 3 items:

Ideally:

- 3 Consumers could run concurrently ✅
- 2 Producers could also run concurrently ✅

But `synchronized` only allows **1 thread at a time**, blocking potential **parallelism**.

We need more refined control.

---

## 🧠 Observation:

Let’s say:

- Size = 0 → 5 producer threads allowed, 0 consumer threads
- Size = 1 → 4 producer threads allowed, 1 consumer thread
- Size = 3 → 2 producer threads allowed, 3 consumer threads

This leads us to...

---

# 💡 Introducing **Semaphores**

### Visual Model of Permit Control:

- Producer Semaphore permits = 5 (initially store empty)
- Consumer Semaphore permits = 0 (nothing to consume)

### Producer Flow:

```java
producerSemaphore.acquire();
store.addItem();
consumerSemaphore.release();

// working is like this we have a permit of 5 let' say 5 thread comes and we also have permit for producer so all five producer thread will enter and do the work which to add item and it will release five consumer permit because we now we have 5 item 

// so in our below consumer flow what will happen is that suppose three thread comes total permit that our consumer has 5 permit and producer permit is 0 , so all three consumer thread will enter and consumer item from store , 5-3 = 2 after three consumer thread consumption what will happen we have 2 empty space for producer to fill so the cycle continues 

```

### Consumer Flow:

```java
consumerSemaphore.acquire();
store.removeItem();
producerSemaphore.release();

```

### Example Walkthrough:

- Producer acquires → permit--
- Adds item → now consumers allowed to consume
- Releases consumer permit → consumerSemaphore++

Same flow applies to consumer side in reverse.

---

## ✅ Semaphore-Based Code

```java
public class Client {
    public static void main(String[] args) {
        Semaphore producerSemaphore = new Semaphore(5); // Store empty, allow 5 producers
        Semaphore consumerSemaphore = new Semaphore(0); // Nothing to consume yet

        ExecutorService es = Executors.newCachedThreadPool();
        Store store = new Store(5);

        for (int i = 1; i <= 8; i++) {
            es.execute(new Producer(store, producerSemaphore, consumerSemaphore));
        }

        for (int i = 1; i <= 20; i++) {
            es.execute(new Consumer(store, producerSemaphore, consumerSemaphore));
        }
    }
}

```

⛔ **But still won’t work fully!** Why?

We’re using `ArrayList`, which is **not thread-safe** ⚠️

---

## ✅ Fix: Use **Concurrent Data Structures**

- Use `ConcurrentLinkedQueue` or `BlockingQueue`
- These are thread-safe, no need for manual synchronization

---

# 💥 Bonus Concept: **Deadlock**

### Scenario:

```java
Thread 1:
    lock1.lock();
    lock2.lock();

Thread 2:
    lock2.lock();
    lock1.lock();

```

If:

- Thread 1 acquires lock1 → context switch
- Thread 2 acquires lock2 → context switch

Now:

- Thread 1 waits for lock2 held by Thread 2
- Thread 2 waits for lock1 held by Thread 1

⚰️ **Both threads are stuck forever** → **Deadlock**

---