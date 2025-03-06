# SOLID Principles in Object-Oriented Programming

## Introduction
SOLID is an acronym representing five design principles that help software developers create more maintainable, scalable, and robust object-oriented applications. These principles were introduced by Robert C. Martin (Uncle Bob) to improve software design and architecture.

### SOLID Principles:
1. **Single Responsibility Principle (SRP)**
2. **Open/Closed Principle (OCP)**
3. **Liskov Substitution Principle (LSP)**
4. **Interface Segregation Principle (ISP)**
5. **Dependency Inversion Principle (DIP)**

---

## 1. Single Responsibility Principle (SRP)

**Definition:** A class should have only one reason to change. This means a class should have only one responsibility or function.

**Explanation:** If a class handles multiple responsibilities, changes in one responsibility might impact others, leading to unintended side effects.

**Example:**
```java
class Invoice {
    private double amount;
    
    public void calculateTotal() {
        // Calculation logic
    }
    
    public void printInvoice() {
        // Printing logic (violates SRP)
    }
}
```
**Improved Version:**
```java
class Invoice {
    private double amount;
    
    public void calculateTotal() {
        // Calculation logic
    }
}

class InvoicePrinter {
    public void printInvoice(Invoice invoice) {
        // Printing logic
    }
}
```
Here, `Invoice` handles calculations, and `InvoicePrinter` takes care of printing, adhering to SRP.

---

## 2. Open/Closed Principle (OCP)

**Definition:** Software entities (classes, modules, functions) should be open for extension but closed for modification.

**Explanation:** You should be able to add new functionality without modifying existing code, ensuring maintainability.

**Example (Violation of OCP):**
```java
class PaymentProcessor {
    public void processPayment(String paymentType) {
        if (paymentType.equals("CreditCard")) {
            // Process credit card
        } else if (paymentType.equals("UPI")) {
            // Process UPI
        }
    }
}
```
**Improved Version (OCP Compliance using Polymorphism):**
```java
interface Payment {
    void pay();
}

class CreditCardPayment implements Payment {
    public void pay() {
        // Process credit card payment
    }
}

class UpiPayment implements Payment {
    public void pay() {
        // Process UPI payment
    }
}

class PaymentProcessor {
    public void processPayment(Payment payment) {
        payment.pay();
    }
}
```
Now, new payment methods can be added without modifying `PaymentProcessor`.

---

## 3. Liskov Substitution Principle (LSP)

**Definition:** If Class B is a subtype of Class A ,then we should be able to replace object of A with B without breaking the behaviour of program 

**Explanation:** A derived class should extend the base class without changing its behavior.

**Example (Violation of LSP):**
```java
class Rectangle {
    protected int width, height;
    
    public void setWidth(int width) { this.width = width; }
    public void setHeight(int height) { this.height = height; }
    public int getArea() { return width * height; }
}

class Square extends Rectangle {
    public void setWidth(int width) {
        super.setWidth(width);
        super.setHeight(width);
    }
    public void setHeight(int height) {
        super.setWidth(height);
        super.setHeight(height);
    }
}
```
Here, `Square` modifies `Rectangle`'s behavior, violating LSP.

**Correct Approach:** Instead of inheritance, use composition or a separate hierarchy for `Square` and `Rectangle`.

---

## 4. Interface Segregation Principle (ISP)

**Definition:** A class should not be forced to implement interfaces it does not use.

**Explanation:** Instead of one large interface, create multiple small, specific interfaces.

**Example (Violation of ISP):**
```java
interface Worker {
    void work();
    void eat();
}

class Robot implements Worker {
    public void work() {
        // Working logic
    }
    public void eat() {
        // Robots don’t eat! (ISP violation)
    }
}
```
**Improved Version (ISP Compliance):**
```java
interface Workable {
    void work();
}

interface Eatable {
    void eat();
}

class Robot implements Workable {
    public void work() {
        // Working logic
    }
}

class Human implements Workable, Eatable {
    public void work() {
        // Working logic
    }
    public void eat() {
        // Eating logic
    }
}
```
Now, `Robot` is not forced to implement `eat()`, adhering to ISP.

---

## 5. Dependency Inversion Principle (DIP)

**Definition:** High-level modules should not depend on low-level modules. Both should depend on abstractions.

**Explanation:** Instead of directly depending on concrete implementations, use interfaces or abstractions.

**Example (Violation of DIP):**
```java
class Light {
    public void turnOn() {}
    public void turnOff() {}
}

class Switch {
    private Light light;
    
    public Switch(Light light) {
        this.light = light;
    }
    public void operate() {
        light.turnOn();
    }
}
```
Here, `Switch` is tightly coupled with `Light`.

**Improved Version (DIP Compliance):**
```java
interface Switchable {
    void turnOn();
    void turnOff();
}

class Light implements Switchable {
    public void turnOn() {}
    public void turnOff() {}
}

class Fan implements Switchable {
    public void turnOn() {}
    public void turnOff() {}
}

class Switch {
    private Switchable device;
    
    public Switch(Switchable device) {
        this.device = device;
    }
    public void operate() {
        device.turnOn();
    }
}
```
Now, `Switch` can work with `Light`, `Fan`, or any `Switchable` device, reducing dependency.

---

## Conclusion
The SOLID principles provide a foundation for writing better object-oriented code. Following these principles ensures:
- **Maintainability** (Easier to modify and extend code)
- **Scalability** (Easier to add new features)
- **Testability** (Easier to test code independently)
- **Code Reusability** (Encourages modularity)

By adhering to SOLID principles, developers can build software that is robust, flexible, and future-proof.
