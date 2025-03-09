# Observer Design Pattern - E-commerce "Notify Me" Feature

## What is the Observer Pattern?
The **Observer Pattern** is a **behavioral design pattern** that defines a **one-to-many dependency** between objects. When the **subject (observable)** changes its state, **all its dependents (observers)** are notified automatically.

## Key Concepts:
- **Subject (Observable):** Maintains a list of observers and notifies them of any state changes.
- **Observers:** Users who have subscribed to be notified when an item is back in stock.
- **Loose Coupling:** Observers and subjects interact via an interface, making the system more flexible.

---

## Observer Pattern – Implementation for an E-commerce "Notify Me" Feature

### **Step 1: Create an Observer Interface**
```java
interface Observer {
    void update(String productName);
}
```

### **Step 2: Create a Subject Interface**
```java
import java.util.ArrayList;
import java.util.List;

interface Subject {
    void addObserver(Observer observer);
    void removeObserver(Observer observer);
    void notifyObservers();
}
```

### **Step 3: Implement the Concrete Subject (Product Stock Manager)**
```java
class Product implements Subject {
    private List<Observer> observers = new ArrayList<>();
    private String productName;
    private boolean inStock = false;

    public Product(String productName) {
        this.productName = productName;
    }

    public void setInStock(boolean inStock) {
        this.inStock = inStock;
        if (inStock) {
            notifyObservers();
        }
    }

    @Override
    public void addObserver(Observer observer) {
        observers.add(observer);
    }

    @Override
    public void removeObserver(Observer observer) {
        observers.remove(observer);
    }

    @Override
    public void notifyObservers() {
        for (Observer observer : observers) {
            observer.update(productName);
        }
    }
}
```

### **Step 4: Implement Concrete Observers (Customers who subscribed for notifications)**
```java
class Customer implements Observer {
    private String name;

    public Customer(String name) {
        this.name = name;
    }

    @Override
    public void update(String productName) {
        System.out.println("Hello " + name + ", the product '" + productName + "' is back in stock!");
    }
}
```

### **Step 5: Demonstrate the Observer Pattern in an E-commerce Scenario**
```java
public class ObserverPatternEcommerceDemo {
    public static void main(String[] args) {
        Product product = new Product("Samsung Galaxy S24 Ultra");

        Observer customer1 = new Customer("Alice");
        Observer customer2 = new Customer("Bob");

        product.addObserver(customer1);
        product.addObserver(customer2);

        // When the product is back in stock, notify all subscribed customers
        System.out.println("Product is now available...");
        product.setInStock(true);
    }
}
```

---

## Benefits of the Observer Pattern in E-commerce
✔ **Automatic Notifications:** Customers receive updates as soon as the product is in stock.  
✔ **Decoupled System:** The product stock logic and user notifications are independent.  
✔ **Scalable:** More users can subscribe without modifying existing code.  
✔ **Real-time Alerts:** Improves customer satisfaction with instant stock availability updates.  

By implementing the Observer Pattern, we create a **robust and user-friendly "Notify Me" feature** in an e-commerce system.
