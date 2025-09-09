# Process and Thread

## 💻 Process, Thread & Concurrency — Enhanced Notes

---

### 📦 Process & Program

- When we **download software or a game**, we get a `.exe` file — this is a **compiled file**.
- That compiled file is a **program**.
- When this program is loaded into **RAM and starts running**, it becomes a **Process**.
- So, **Process = Program in execution**.

---

### 🧠 Understanding Process

- Every running software (e.g., **GTA 5**, **Chrome**) is a **process**.
- Behind every process, there’s code written by someone — when executed, it becomes a **running instance** (process).
- We can **see all running processes** in the **Task Manager**.

### 📋 Process Control Block (PCB)

- OS uses **PCB** to track all active processes.
- PCB contains:
    - `Process ID`
    - `Priority`
    - `Memory usage`
    - `List<Variables>`
    - `Call stack`
    - `Program counter` (tracks **last line of code** executed)

> ✅ We can even assign higher priority to some processes (e.g., antivirus, background downloads).
> 

---

### 🔍 Going Inside the Process

- 🔧 Processor **executes threads**, not entire processes.
- A **process contains multiple threads**.
- All threads inside a process **share the same memory**.

### 📄 Example: MS Word Process

- When using **MS Word**:
    - Input handling thread: `Thread-1` — handles typing
    - Output thread: `Thread-2` — updates UI
    - Spell check thread: `Thread-3`
    - Auto-save thread: `Thread-4`

> Threads run in parallel (virtually) inside the same process, sharing memory.
> 

### 🧠 Shared Memory Usage

- If **Thread-1 (input)** stores data you typed,
- Then **Thread-3 (spell check)** needs access to that data — **shared memory** allows this.

---

### 🧾 Clarifying PCB Again

- At **process level**, PCB has:
    - `Process ID`, `Priority`, `Memory`, `Variable list`
- Each **thread** inside the process has:
    - **Own `Program Counter` & `Call Stack`**

---

### 🍽️ Analogy: Process vs Thread

### Process = Hotel Kitchen

- A hotel kitchen has **equipment (memory, oven, etc.)**

### Threads = Chefs

- Chefs (threads) use shared equipment to make different dishes (tasks).
- Each chef performs a different job, **using the same kitchen**.

---

### 🔄 What is Concurrency?

### Kitchen Analogy:

- You’re a chef with **one burner (single-core processor)**.
- You need to cook:
    - Pasta
    - Juice
    - Rice

### ❌ Option 1: One dish at a time

- Cook pasta fully → cook juice → cook rice
- **Not efficient**. Imagine:
    - You're listening to music, then typing in Word. If one stops completely — bad UX.

### ✅ Option 2: Concurrency

- Chef cooks pasta for 2 mins
- Switches to juice prep for 1 min
- Then rice
- Then back to pasta
- Feels like **all are cooking together**, but actually — only **one is being cooked at any moment**

> 🎯 This is Concurrency — multiple threads are in a partial state, but only one runs at a time on a single-core system.
> 

---

### 🧠 Real System Example: Single Core

- You’re listening to **YouTube music** and writing in **MS Word**.
- Music = Process 1 (Thread A), Word = Process 2 (Thread B)
- On a **single-core CPU**, only **one thread runs at a time**
- What OS does:
    - Run **music thread** for 10 ms
    - Save state (program counter)
    - Switch to **word thread** for 10 ms
    - Save state
    - Return to music thread

> ⚙️ This switching is called Context Switching
> 

### ⏱️ Time Overhead

- Switching takes a few **milliseconds**:
    - Save state of T1
    - Load state of T2
    - Resume execution
- So, `Time(T1) + Time(T2)` is **slightly > T3** (a pure uninterrupted thread)

---

### ⚡ Multi-Core Processor

- In multi-core, threads can **actually** run **simultaneously**
- E.g., Thread A runs on Core-1, Thread B runs on Core-2
- Gives **real parallelism**, not just concurrency

---

### 🧑‍💻 Thread Implementation in Java

```java
class HelloWorld implements Runnable {
    @Override
    public void run() {
        System.out.println("Hello from thread!");
    }
}

Runnable h1 = new HelloWorld();
for (int i = 0; i < 100; i++) {
    Thread t = new Thread(h1);
    t.start();
}

```

✅ This code creates **100 threads**, all printing "Hello from thread!"

---

### 🧠 Final Thoughts

- **Process** = Program in execution
- **Thread** = Lightweight task inside a process
- Threads share memory of their parent process
- **Concurrency** is the illusion of multitasking on single-core
- **Context Switching** is the mechanism behind the illusion
- **Multicore = True Parallelism**