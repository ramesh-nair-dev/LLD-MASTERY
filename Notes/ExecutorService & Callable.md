# ExecutorService & Callable

---

## 🧵 Thread Basics in Java

### ✅ Creating a Thread:

To create a thread, you first **need a task** — only then can a thread exist. A **thread is simply a piece of code that runs in parallel**.

### 🧩 Step-by-step Example:

```java
class NumberPrinter implements Runnable {
    int num;

    NumberPrinter(int num) {
        this.num = num;
    }

    @Override
    public void run() { // this is the abstract method of Runnable
        System.out.println(this.num);
    }
}

```

### 🚀 Client Class

```java
class Client {
    public static void main(String[] args) {
        for (int i = 1; i < 1000; i++) {
            Runnable obj = new NumberPrinter(i);
            Thread th = new Thread(obj);
            th.start(); // Thread gets created here
        }
    }
}

```

### 🧠 What’s happening?

We are printing numbers from 1 to 1000, but each number is being printed by a separate thread.

- Thread 1 → prints 1
- Thread 2 → prints 2
- ...
- Thread 1000 → prints 1000

So **1000 threads** are created and run in parallel. But...

> ❗ What if tomorrow I want to print 10,000,000 numbers? Creating 10M threads is highly inefficient and CPU-intensive.
> 

---

## ⚙️ Why ExecutorService is Better

### Problem with above approach:

Creating too many threads = 🚨 BAD for performance and system stability.

### ✅ Solution: `ExecutorService`

Instead of creating a thread for every small task (like printing a number), we use an **ExecutorService** with a **thread pool**.

**Example goal**: Print numbers from 1 to 100 using only 10 threads.

```java
ExecutorService executor = Executors.newFixedThreadPool(10);

```

- ExecutorService maintains a **task queue**.
- First 10 tasks are assigned to the 10 threads in the pool.
- As threads finish tasks, they pick new ones from the queue.

### Example flow:

- Task 1-10 → Threads 1-10
- Task 11 → Thread 3 (if it's free)
- Task 12 → Thread 6 (if it's free)
- ... and so on.

---

## 🔁 Other Thread Pool Types

### 🧠 `CachedThreadPool`

- Creates new threads **dynamically** if all current threads are busy.
- Good for unpredictable workloads.
- Helps **avoid bottlenecks** when thread demand spikes suddenly.

> 🔥 Instead of creating 1 million threads, we can limit it to, say, 40 threads — saves CPU & JVM overhead.
> 

---

## 🧵 Runnable Limitation

Runnable uses:

```java
public void run() { }

```

- Since it returns `void`, **it can't return any result** back from the thread.

---

## ✅ Use `Callable` if Return Type is Needed

```java
class MyTask implements Callable<Integer> {
    @Override
    public Integer call() throws Exception {
        // do some work
        return result;
    }
}

```

- Callable can return any type: Integer, String, List, etc.
- Used with `Future<T>` to capture return value.

---

## 🧠 Merge Sort using Callable — Full Explanation

### 💡 Problem:

Sort an array using merge sort, but have the left and right halves execute in **parallel threads** for faster execution.

---

### ✅ Step-by-step Code Explanation

```java
class Sorter implements Callable<List<Integer>> {
    private List<Integer> arr;

    Sorter(List<Integer> arr) {
        this.arr = arr;
    }

    @Override
    public List<Integer> call() throws Exception {
        // ✅ Base condition: single element is always sorted
        if (arr.size() <= 1) return arr;

        // ✅ Split array into left and right halves
        int mid = arr.size() / 2;
        List<Integer> leftSide = new ArrayList<>();
        List<Integer> rightSide = new ArrayList<>();

        for (int i = 0; i < mid; i++) leftSide.add(arr.get(i));
        for (int i = mid; i < arr.size(); i++) rightSide.add(arr.get(i));

        // ✅ Create tasks for left & right sorting
        Sorter leftSorter = new Sorter(leftSide); // task 1
        Sorter rightSorter = new Sorter(rightSide); // task 2

        ExecutorService executor = Executors.newFixedThreadPool(2);

        // ✅ Submit tasks and get Futures
        Future<List<Integer>> leftFuture = executor.submit(leftSorter);
        Future<List<Integer>> rightFuture = executor.submit(rightSorter);

        // ✅ Retrieve sorted results
        List<Integer> leftSorted = leftFuture.get();
        List<Integer> rightSorted = rightFuture.get();

        executor.shutdown();

        // ✅ Merge using 2-pointer
        List<Integer> merged = new ArrayList<>();
        int i = 0, j = 0;
        while (i < leftSorted.size() && j < rightSorted.size()) {
            if (leftSorted.get(i) <= rightSorted.get(j)) {
                merged.add(leftSorted.get(i++));
            } else {
                merged.add(rightSorted.get(j++));
            }
        }

        while (i < leftSorted.size()) merged.add(leftSorted.get(i++));
        while (j < rightSorted.size()) merged.add(rightSorted.get(j++));

        return merged;
    }
}

```

### 🧪 Client Code:

```java
class Client {
    public static void main(String[] args) throws Exception {
        List<Integer> arr = Arrays.asList(6, 5, 4, 3, 2, 1);
        Sorter sorter = new Sorter(arr);

        ExecutorService executor = Executors.newFixedThreadPool(1);
        Future<List<Integer>> result = executor.submit(sorter);

        System.out.println("Sorted output: " + result.get());
        executor.shutdown();
    }
}

```

---

## ⚠️ Thread Safety and Shared Resources

### ❓Problem:

When multiple threads update a **shared variable**, race conditions occur.

```java
class Value {
    public int value;
    Value(int v) { this.value = v; }
}

```

### 🧵 Adder Thread

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

```

### ➖ Subtractor Thread (same logic):

```java
class Subtractor implements Runnable {
    private Value v;

    Subtractor(Value v) { this.v = v; }

    public void run() {
        for (int i = 1; i <= 100; i++) {
            this.v.value = this.v.value - 1;
        }
    }
}

```

### 🔍 What Happens Behind the Scenes:

1. Each thread reads `v.value` into a temp register.
2. Performs arithmetic.
3. Writes result back to `v.value`.

🧠 If both threads do this in parallel:

- Adder sets v = 1
- Subtractor sets v = -1

Final value may end up being -1 instead of 0. This is a classic **race condition**.

---