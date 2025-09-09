# Lamda and Streams.

---

# 📚 Polished Notes: Streams and Lambda

---

### Threads and Runnable

```java
class HelloPrinter implements Runnable {

    public void run() {
        System.out.println("Hello world");
    }
}

class Client {
    public static void main(String[] args) {

        Runnable r = new HelloPrinter();
        Thread th = new Thread(r);

        for (int i = 0; i < 100; i++) {
            th.start();
        }
    }
}

```

So what are we doing here?

I want to print "Hello world" 100 times, but I want to execute this task using all different threads.

To make a thread work, there are 2 requirements:

- We should have a task.
- That task should implement the Runnable interface and override the run method.

Here, we are printing "Hello world" 100 times by different threads running concurrently.

But what was our intention?

Our goal was to print "Hello world" in different threads.

And for thread creation, we need two things:

1. We need a task (i.e., a class that implements the Runnable interface).
2. Runnable is a functional interface, meaning it has only a single abstract method, which is `run()`.

Now in the client class, what did we do?

```java
Runnable r = new HelloPrinter();

```

Why did we do this? Because:

In the next line:

```java
Thread th = new Thread(r);

```

we pass this Runnable object while creating the thread.

Inside the Thread class, the constructor expects a Runnable object to call its `run` method when `start()` is called.

That’s why we wrote:

```java
Runnable r = new HelloPrinter();

```

Here, `HelloPrinter` implements the Runnable interface.

Meaning analogy-wise: `HelloPrinter` is a child, and Runnable is a parent.

A parent reference can point to a child object — that’s allowed.

And for thread creation, we also need a Runnable object — that's why we do `Runnable r = new HelloPrinter()`.

---

Did you notice one thing?

We just passed a Runnable object and implemented the run method, but to achieve this small thing, we created an entire new class `HelloPrinter`.

Don’t you think it’s hectic because this class is only used here and nowhere else?

---

### Anonymous Class

So what can be the solution?

We have something called an **anonymous class**.

We directly do something inside our client class:

```java
class Client {
    public static void main(String[] args) {

        Runnable r = new Runnable() {
            public void run() {
                System.out.println("Hello world");
            }
        };
    }
}

```

So what is this?

It is nothing but an **anonymous class**.

We are doing the same thing we did earlier:

- Implementing Runnable.
- Overriding the run method.

Here, instead of creating a separate class, we directly define and instantiate it on the spot.

On the LHS, we have `Runnable r =` because for the thread, we need a Runnable object.

This anonymous class follows exactly the same properties as a class:

- We can define data members and methods inside it.
- But the main thing: to create a thread, we must override the `run` method.

Now:

```java
Thread th = new Thread(r);

for (int i = 0; i < 100; i++) {
    th.start();
}

```

This will work like a charm.

---

When should we use anonymous classes?

When there are tasks that are used once or are not used many times, we can create anonymous classes.

In our case, for creating a thread, we need a Runnable object.

That Runnable object should override the run method — that's our thread requirement.

One way is to create a separate class `HelloPrinter`, implement Runnable, and override `run()`.

But that class won’t be used further anywhere — just to fulfill thread requirement, we did all these things.

Instead, with anonymous classes, we can simplify.

---

We can also write it even more simply inside the client class:

```java
Thread th = new Thread(new Runnable() {
    @Override
    public void run() {
        System.out.println("Hello world");
    }
});

```

Earlier example:

```java
Runnable r = new Runnable() {
    public void run() {
        System.out.println("Hello world");
    }
};

```

then passed `r` in `new Thread(r)`.

Now, we are directly passing the Runnable on the RHS inside `new Thread()` — shorthand way.

---

This is working because Runnable is a functional interface (only one abstract method).

Java automatically maps it.

---

### Lambda Expressions

We have one more shorthand:

Before:

```java
public void run() {
    System.out.println("Hello world");
}

```

This function does not take any arguments from the user.

Now, using lambda:

```java
Thread th = new Thread(() -> {
    System.out.println("Hello world");
});

```

This shorthand is called a **lambda expression**.

---

```java
public class Student {
    int age;
    double psp;
    String name;
    double attendance;

    Student(int age, double psp, String name, double attendance) {
        this.age = age;
        this.psp = psp;
        this.name = name;
        this.attendance = attendance;
    }

    @Override
    public String toString() {
        return "Student{" +
                "age=" + this.age +
                ", psp=" + this.psp +
                ", name='" + this.name + '\'' +
                ", attendance=" + this.attendance +
                '}';
    }
}

```

You have the Student class object.

---

In client side:

```java
class Client {
    public static void main(String[] args) {
        List<Student> ls = new ArrayList<>();
        // Add few students

```

Now, I want to sort them based on age.

We use **Collections.sort()** because the sorting we want is **custom**, so we need **Collection.sort()**.

`Collections.sort(list, Comparator)` — inside we pass the list and the Comparator object.

Inside the Comparator object, we have a function interface `compare`,

and that functional interface has a single abstract method called `compare()`,

where we write our logic.

---

We have two ways to do this:

**1. Create a separate class StudentAgeSort implementing Comparator Interface**

and override the compare method there.

**2. Use lambda directly:**

```java
Collections.sort(ls, (a, b) -> {
    return a.age - b.age;
});

```

That is simple.

Tomorrow if we want to sort based on PSP:

If we go through method 1, we need to create another class implementing Comparator and override again — unnecessary class creation.

Instead:

```java
Collections.sort(ls, (a, b) -> {
    return (int)(a.psp - b.psp);
});

```

Simple.

---

This is all about **lambda**.

---

### Streams

Let’s understand about streams.

Stream is nothing but a stream.

Consider it like a container — with one inlet and one outlet.

Through the inlet, it receives a stream of data, inside the container it processes the data, and through the outlet, it gives processed data.

Inside streams, we have two types of operations:

1. **Intermediate operations** — here we don’t get processed data immediately.
2. **Terminal operations** — here we get processed data; once a terminal operation is done, we can't use the stream again.

Example:

```java
List<Integer> l2 = List.of(5, 10, 2, 1, 16, 3, 8, 1, 2, 2);

```

This is the list we have.

Now, if we want to print all elements one by one,

we can use a traditional for loop,

but we can also go for stream.

```java
Stream<Integer> s1 = l2.stream(); // convert l2 list to streams

l2.forEach((e) -> {
    System.out.println(e);
});

```

This will print all elements.

---

What is happening here?

`forEach` requires:

```java
void forEach(Consumer<? super T> action)

```

Consumer is a functional interface — it has a single abstract method.

To implement it, we have two ways:

- Create a separate class implementing Consumer.
- Or directly use a lambda.

---

```java
l2.limit(4).forEach((e) -> {
    System.out.println(e);
});

```

What this will do:

It will limit the elements to 4 and print only those 4 elements.

---

```java
List<Student> list1 = new ArrayList<>();

list1.add(new Student(25, 65.0, "Mohit", 10.0));
list1.add(new Student(21, 78.0, "Nishant", 100.0));
list1.add(new Student(28, 20.0, "Rohan", 90.0));

```

Now:

```java
list1.stream().filter((e) -> {
    return e.psp > 50;
}).limit(2).forEach((e) -> {
    System.out.println(e);
});

```

What is happening here:

- We filter students having PSP greater than 50.
- From that, we only take 2 students (limit 2).
- Then we print them.

**Important:** To run intermediate operations, we must have a terminal operation.

---

```java
int count = (int) l2.stream().filter(e -> {
    // System.out.println("Inside Filter" + e);
    return e % 2 == 0;
}).count();
System.out.println(count);

```

Here, what are we doing:

We are filtering all even numbers and then counting them.

How is it working internally?

- First element enters the filter.
- It checks whether it’s even.
- If yes, passes forward.
- Finally, the count function counts them.

**Important:**

Ideally, we might think first all elements are filtered, then count happens — but it does not work that way.

Streams process **element-by-element** (lazy evaluation).

---

Another example:

```java
Stream<Integer> s1 = l2.stream().map(e -> {
    System.out.println("Inside Map" + e);
    return e * e;
});

int count = (int) s1.count();

```

Suppose we have an array [1,2,3].

After `map`, it becomes [1,4,9].

But since we are doing a count, the mapped values are not used.

**Important:**

- Intermediate operations like `map` are only triggered when a terminal operation (like `count`) is called.
- In this case, count triggers it.
- But if count doesn't need the mapped value, it doesn't use the modified values.

Earlier, it was going inside filter because the count changed depending on the filter.

---

#