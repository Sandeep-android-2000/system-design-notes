# Design Pattern and Strategy Design Pattern

## What is a Design Pattern?
A **design pattern** is a reusable solution to a common software design problem. It provides best practices and templates that developers can follow to solve recurring problems in software architecture.

### **Key Characteristics of Design Patterns:**
- **Reusable:** Helps solve problems that occur repeatedly in different projects.
- **Best Practices:** Based on proven and efficient solutions.
- **Flexible & Scalable:** Improves maintainability and extensibility of code.
- **Language-Independent:** Can be implemented in any object-oriented programming language.

### **Types of Design Patterns:**
1. **Creational Patterns** – Deal with object creation (e.g., Singleton, Factory, Builder).
2. **Structural Patterns** – Define relationships between objects (e.g., Adapter, Decorator, Composite).
3. **Behavioral Patterns** – Handle object interactions and responsibilities (e.g., Strategy, Observer, Command).

---

## What is the Strategy Design Pattern?
The **Strategy Pattern** is a **behavioral design pattern** that allows a family of algorithms to be defined and encapsulated in separate classes, making them interchangeable at runtime.

### **Purpose:**
- Helps define multiple algorithms (strategies) for a particular task.
- Allows the client to select a specific algorithm at runtime.
- Promotes **Open/Closed Principle** (OCP) by avoiding modifications to existing code when adding new strategies.

---

## Strategy Design Pattern – Implementation in Java

### **Step 1: Create a Strategy Interface**
```java
interface PaymentStrategy {
    void pay(int amount);
}
```

### **Step 2: Implement Different Strategies**
```java
class CreditCardPayment implements PaymentStrategy {
    public void pay(int amount) {
        System.out.println("Paid " + amount + " using Credit Card.");
    }
}

class PayPalPayment implements PaymentStrategy {
    public void pay(int amount) {
        System.out.println("Paid " + amount + " using PayPal.");
    }
}
```

### **Step 3: Create a Context Class**
```java
class PaymentContext {
    private PaymentStrategy paymentStrategy;

    public void setPaymentStrategy(PaymentStrategy paymentStrategy) {
        this.paymentStrategy = paymentStrategy;
    }

    public void executePayment(int amount) {
        paymentStrategy.pay(amount);
    }
}
```

### **Step 4: Use the Strategy Pattern in a Client**
```java
public class StrategyPatternDemo {
    public static void main(String[] args) {
        PaymentContext context = new PaymentContext();
        
        // Using Credit Card Strategy
        context.setPaymentStrategy(new CreditCardPayment());
        context.executePayment(500);
        
        // Switching to PayPal Strategy
        context.setPaymentStrategy(new PayPalPayment());
        context.executePayment(1000);
    }
}
```

---

## Benefits of Strategy Pattern
✔ **Encapsulation of Algorithms:** Different strategies are in separate classes.  
✔ **Easily Extendable:** New strategies can be added without modifying existing code.  
✔ **Improves Maintainability:** Reduces complexity by separating concerns.  
✔ **Runtime Flexibility:** Algorithms can be changed dynamically.

By using the Strategy Design Pattern, developers can write cleaner, more modular, and flexible code.
