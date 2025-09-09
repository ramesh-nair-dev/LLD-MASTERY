# Strategy and Observer

---

### ✅ Today’s Focus:

- **Strategy Pattern**
- **Observer Pattern**

We'll walk through real-world analogies, design evolution, SOLID principle violations, step-by-step memory-level behavior, and use UML diagrams for clarity.

---

## 🧭 Strategy Pattern

### 🧠 Concept:

> Strategy Pattern defines a family of algorithms, encapsulates each one, and makes them interchangeable.
> 

You select the algorithm dynamically based on user input or context.

---

### 🎯 Real-life Analogy: Google Maps

You enter **source** and **destination**, and choose a mode of transportation: Car, Bike, or Walk. Google dynamically applies the correct route-finding algorithm.

---

### ❌ Version 1: Hardcoded if-else

```java
class GoogleMaps {
    void findPath(String source, String destination, String mode) {
        if(mode.equals("Car")) {
            // Car path logic
        } else if(mode.equals("Bike")) {
            // Bike path logic
        } else if(mode.equals("Walk")) {
            // Walk path logic
        }
    }
}

```

### 🔍 Problems:

- **SRP violated** – Routing logic is mixed with GoogleMaps class responsibility.
- **OCP violated** – Adding a new mode requires modifying the class.
- **DIP violated** – High-level class is directly dependent on low-level implementations.

---

### ⚙️ Version 2: Separated Logic, Still Coupled

```java
class CarPath {
    void carPathAlgo() { /* ... */ }
}

class Google {
    void findPath(String source, String destination, String mode) {
        if(mode.equals("Car")) {
            new CarPath().carPathAlgo();
        }
        // Same for Bike and Walk
    }
}

```

### 🔍 Issues Persist:

- **SRP fixed**, but
- **OCP** and **DIP still violated** due to tight coupling.

---

### ✅ Final Version: Strategy Pattern

### Step 1: Interface

```java
public interface PathCalculator {
    void findPath(String source, String destination);
}

```

### Step 2: Implementations

```java
class CarPathCalculator implements PathCalculator {
    public void findPath(String source, String destination) {
        // Car path algorithm
    }
}

class BikePathCalculator implements PathCalculator {
    public void findPath(String source, String destination) {
        // Bike path algorithm
    }
}

class WalkPathCalculator implements PathCalculator {
    public void findPath(String source, String destination) {
        // Walk path algorithm
    }
}

```

### Step 3: Factory

```java
class PathCalculatorFactory {
    public static PathCalculator getPathCalculator(String mode) {
        switch(mode) {
            case "Car": return new CarPathCalculator();
            case "Bike": return new BikePathCalculator();
            case "Walk": return new WalkPathCalculator();
            default: throw new IllegalArgumentException("Invalid mode");
        }
    }
}

```

### Step 4: Context Class

```java
class GoogleMaps {
    public void findPath(PathCalculator strategy, String source, String destination) {
        strategy.findPath(source, destination);
    }
}

```

### Step 5: Client Code

```java
GoogleMaps g = new GoogleMaps();
PathCalculator pc = PathCalculatorFactory.getPathCalculator("Car");
g.findPath(pc, "A", "B");

```

### ✅ Benefits:

- Follows **SRP**, **OCP**, and **DIP**.
- Flexible and interchangeable algorithms.

### 🧠 Bonus Insight:

- Strategy can be changed **at runtime** dynamically.
- Used in compression tools (ZIP, RAR), payment gateways (UPI, Card, Wallet).

### 📊 UML Diagram – Strategy Pattern:

```
     +------------------+
     |  PathCalculator  |<-----------------------------+
     +------------------+                              |
             ^                                           |
             |                                           |
 +----------------------+   +----------------------+   +----------------------+
 | CarPathCalculator    |   | BikePathCalculator   |   | WalkPathCalculator   |
 +----------------------+   +----------------------+   +----------------------+
             ^
             |  uses
     +------------------+
     |   GoogleMaps     |-------------------------------->
     +------------------+
     | +findPath(...)   |
     +------------------+

```

---

## 👁️ Observer Pattern

### 🧠 Concept:

> Observer Pattern defines a one-to-many dependency. When the subject changes, all dependent observers are notified automatically.
> 

---

### 🎯 Real-life Analogy: Leo Messi on Instagram

- Subject = Messi
- Observers = Followers
- When Messi posts → all followers get notified

---

### ☀️ Problem Context: Weather Station

- A physical weather station collects data.
- This data must be updated in multiple display units (TV, app, signage).

---

### ✅ Solution: Observer Pattern

### Step 1: Subject Interface

```java
public interface Subject {
    void registerObserver(Observer o);
    void removeObserver(Observer o);
    void notifyObservers();
}

```

### Step 2: Observer Interface

```java
public interface Observer {
    void update(float temp, float pressure, float humidity);
}

```

### Step 3: Concrete Subject

```java
class WeatherData implements Subject {
    private List<Observer> observers = new ArrayList<>();
    private float temp, pressure, humidity;

    public void registerObserver(Observer o) {
        observers.add(o);
    }

    public void removeObserver(Observer o) {
        observers.remove(o);
    }

    public void notifyObservers() {
        for (Observer o : observers) {
            o.update(temp, pressure, humidity);
        }
    }

    public void changeMeasurements(float temp, float pressure, float humidity) {
        this.temp = temp;
        this.pressure = pressure;
        this.humidity = humidity;
        notifyObservers();
    }
}

```

### Step 4: Concrete Observer

```java
class MessiObserver implements Observer {
    private float temp, pressure, humidity;

    public void update(float temp, float pressure, float humidity) {
        System.out.println("Before update: " + this.temp + " " + this.pressure + " " + this.humidity);
        this.temp = temp;
        this.pressure = pressure;
        this.humidity = humidity;
        display();
    }

    public void display() {
        System.out.println("Updated Weather: " + temp + ", " + pressure + ", " + humidity);
    }
}

```

### Step 5: Client Code

```java
public class Main {
    public static void main(String[] args) {
        WeatherData weatherData = new WeatherData();
        Observer messi = new MessiObserver();
        weatherData.registerObserver(messi);

        weatherData.changeMeasurements(72.8f, 56.7f, 78.0f);
    }
}

```

### 🔁 Behind the Scenes:

- `WeatherData` holds state and list of observers.
- `changeMeasurements()` updates internal state.
- `notifyObservers()` loops and triggers `update()`.
- `MessiObserver` prints old & new values.

### 📊 UML Diagram – Observer Pattern:

```
     +-------------+             +-------------------+
     |  Subject    |<------------|   WeatherData     |
     +-------------+             +-------------------+
     |+register()  |             | +observers[]      |
     |+remove()    |             | +changeMeasurements|
     |+notify()    |             +-------------------+
                                  |
                                  |
       +-------------+       +------------------+
       |  Observer   |<------|  MessiObserver   |
       +-------------+       +------------------+
       | +update()   |       | +display()       |
       +-------------+       +------------------+

```

---

## ✅ Summary:

| Pattern | Core Use Case | Problem Solved | Key Benefit |
| --- | --- | --- | --- |
| Strategy | Runtime Algo | Violates SRP/OCP/DIP | Flexibility via interfaces |
| Observer | Auto Updates | Notifies many objects on change | Loose coupling, dynamic broadcast |

---

