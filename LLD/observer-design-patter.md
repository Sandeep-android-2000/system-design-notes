# Observer Design Pattern

## What is the Observer Pattern?
The **Observer Pattern** is a **behavioral design pattern** that defines a **one-to-many dependency** between objects. When the **subject (observable)** changes its state, **all its dependents (observers)** are notified automatically.

## Key Concepts:
- **Subject (Observable):** Maintains a list of observers and notifies them of any state changes.
- **Observers:** Objects that want to be notified when the subject changes.
- **Loose Coupling:** Observers and subjects interact via an interface, making the system more flexible.

---

## Observer Pattern – Implementation in Java

### **Step 1: Create an Observer Interface**
```java
interface Observer {
    void update(String message);
}
```

### **Step 2: Create a Subject Interface**
```java
import java.util.ArrayList;
import java.util.List;

interface Subject {
    void addObserver(Observer observer);
    void removeObserver(Observer observer);
    void notifyObservers(String message);
}
```

### **Step 3: Implement the Concrete Subject (Observable)**
```java
class NewsAgency implements Subject {
    private List<Observer> observers = new ArrayList<>();

    @Override
    public void addObserver(Observer observer) {
        observers.add(observer);
    }

    @Override
    public void removeObserver(Observer observer) {
        observers.remove(observer);
    }

    @Override
    public void notifyObservers(String message) {
        for (Observer observer : observers) {
            observer.update(message);
        }
    }
}
```

### **Step 4: Implement Concrete Observers**
```java
class NewsChannel implements Observer {
    private String name;

    public NewsChannel(String name) {
        this.name = name;
    }

    @Override
    public void update(String message) {
        System.out.println(name + " received update: " + message);
    }
}
```

### **Step 5: Demonstrate the Observer Pattern**
```java
public class ObserverPatternDemo {
    public static void main(String[] args) {
        NewsAgency agency = new NewsAgency();
        
        Observer channel1 = new NewsChannel("Channel 1");
        Observer channel2 = new NewsChannel("Channel 2");

        agency.addObserver(channel1);
        agency.addObserver(channel2);

        agency.notifyObservers("Breaking News: Observer Pattern Implemented!");
        
        agency.removeObserver(channel1);
        
        agency.notifyObservers("Update: Channel 1 unsubscribed!");
    }
}
```

---

## Benefits of the Observer Pattern
✔ **Loosely Coupled:** The subject and observers interact through an interface, making changes easier.  
✔ **Automatic Updates:** All observers receive notifications when the subject changes.  
✔ **Flexible:** New observers can be added dynamically without modifying existing code.  

By implementing the Observer Pattern, we create a **flexible** and **scalable** event-driven system.
