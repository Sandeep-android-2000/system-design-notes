
# 🔄 State Design Pattern

## 📘 What is it?

The **State Design Pattern** is a **behavioral design pattern** that allows an object to change its behavior when its **internal state changes**. It appears as if the object changed its class.

> Think of a **vending machine** – its behavior depends on its current state (e.g., idle, money inserted, dispensing, out of stock).

---

## 🧠 Type of Design Pattern

- **Category**: Behavioral Pattern  
- **Purpose**: Allow an object to behave differently depending on its current state, without changing the object's class.

---

## 📊 UML Diagram

```
     +------------------+
     |     Context      |
     +------------------+
     | - state: State   |
     +------------------+
     | +setState(State) |
     | +request()       |
     +------------------+
            |
            v
     +----------------+
     |    State       |<-----------------------------+
     +----------------+                              |
     | +handle()      |                              |
     +----------------+                              |
            ^                                        ^
     +---------------+                     +----------------+
     | StateA         |                     |  StateB        |
     +---------------+                     +----------------+
     | +handle()      |                     | +handle()      |
     +---------------+                     +----------------+
```

---

## 🧃 Real-World Example: **Vending Machine**

States:
- **IdleState**: Waiting for customer
- **HasMoneyState**: Money inserted
- **DispenseState**: Item is being dispensed
- **OutOfStockState**: No items to sell

Transitions:
- Insert money → change from Idle to HasMoney
- Press dispense → change from HasMoney to Dispense
- Dispense completed → go back to Idle or OutOfStock

---

## 💻 Java Code Example

### 1. `State` Interface
```java
public interface VendingMachineState {
    void insertMoney();
    void selectProduct();
    void dispense();
}
```

---

### 2. Concrete States

```java
public class IdleState implements VendingMachineState {
    public void insertMoney() {
        System.out.println("Money inserted. You can now select a product.");
    }

    public void selectProduct() {
        System.out.println("Insert money first.");
    }

    public void dispense() {
        System.out.println("Insert money and select product first.");
    }
}

public class HasMoneyState implements VendingMachineState {
    public void insertMoney() {
        System.out.println("Money already inserted.");
    }

    public void selectProduct() {
        System.out.println("Product selected. Dispensing...");
    }

    public void dispense() {
        System.out.println("Dispensing product...");
    }
}

public class OutOfStockState implements VendingMachineState {
    public void insertMoney() {
        System.out.println("Machine is out of stock.");
    }

    public void selectProduct() {
        System.out.println("Machine is out of stock.");
    }

    public void dispense() {
        System.out.println("Machine is out of stock.");
    }
}
```

---

### 3. Context Class

```java
public class VendingMachine {
    private VendingMachineState state;

    public VendingMachine() {
        state = new IdleState();
    }

    public void setState(VendingMachineState newState) {
        state = newState;
    }

    public void insertMoney() {
        state.insertMoney();
    }

    public void selectProduct() {
        state.selectProduct();
    }

    public void dispense() {
        state.dispense();
    }
}
```

---

### 4. Usage Example

```java
public class StatePatternDemo {
    public static void main(String[] args) {
        VendingMachine machine = new VendingMachine();

        machine.insertMoney();
        machine.setState(new HasMoneyState());

        machine.selectProduct();
        machine.dispense();

        machine.setState(new OutOfStockState());
        machine.insertMoney();
    }
}
```

---

### ✅ Output

```
Money inserted. You can now select a product.
Product selected. Dispensing...
Dispensing product...
Machine is out of stock.
```

---

## 🧾 Summary

| Feature            | Description                                                |
|--------------------|------------------------------------------------------------|
| Type               | Behavioral                                                  |
| Purpose            | Change behavior based on internal state                    |
| Real-world Example | Vending Machine, Traffic Signal, Media Player              |
| Benefit            | Cleaner code, no big `if-else` or `switch-case` blocks     |
