# State Design Pattern

## 🧠 What is it?

The **State Design Pattern** allows an object to alter its behavior when its internal state changes. The object will appear to change its class.

This pattern is particularly useful when an object must change its behavior at runtime depending on its state.

---

## 🛠️ When to Use

- An object has multiple states, and its behavior changes based on its state.
- You want to avoid large conditionals or switch statements in your code.
- You want to encapsulate state-specific behavior.

---

## 🧃 Real-Life Example: Vending Machine (LLD)

A vending machine has several states:
- No Coin Inserted
- Coin Inserted
- Item Selected
- Dispensed

Each state has different behavior for the same input (e.g., pressing the button).

---

## ✅ Benefits

- Removes complex conditionals.
- Makes code more maintainable and extendable.
- Improves cohesion within state-specific classes.

---

## 🧱 Java Implementation

### 1. `State` Interface

```java
public interface State {
    void insertCoin();
    void selectItem();
    void dispense();
}
```

---

### 2. `NoCoinState` Class

```java
public class NoCoinState implements State {
    private VendingMachine machine;

    public NoCoinState(VendingMachine machine) {
        this.machine = machine;
    }

    @Override
    public void insertCoin() {
        System.out.println("Coin inserted.");
        machine.setState(machine.getHasCoinState());
    }

    @Override
    public void selectItem() {
        System.out.println("Insert coin first.");
    }

    @Override
    public void dispense() {
        System.out.println("Insert coin first.");
    }
}
```

---

### 3. `HasCoinState` Class

```java
public class HasCoinState implements State {
    private VendingMachine machine;

    public HasCoinState(VendingMachine machine) {
        this.machine = machine;
    }

    @Override
    public void insertCoin() {
        System.out.println("Coin already inserted.");
    }

    @Override
    public void selectItem() {
        System.out.println("Item selected.");
        machine.setState(machine.getItemDispensedState());
    }

    @Override
    public void dispense() {
        System.out.println("Select item first.");
    }
}
```

---

### 4. `ItemDispensedState` Class

```java
public class ItemDispensedState implements State {
    private VendingMachine machine;

    public ItemDispensedState(VendingMachine machine) {
        this.machine = machine;
    }

    @Override
    public void insertCoin() {
        System.out.println("Please wait. Dispensing item.");
    }

    @Override
    public void selectItem() {
        System.out.println("Already dispensing item.");
    }

    @Override
    public void dispense() {
        System.out.println("Item dispensed.");
        machine.setState(machine.getNoCoinState());
    }
}
```

---

### 5. `VendingMachine` Context Class

```java
public class VendingMachine {
    private State noCoinState;
    private State hasCoinState;
    private State itemDispensedState;
    private State currentState;

    public VendingMachine() {
        noCoinState = new NoCoinState(this);
        hasCoinState = new HasCoinState(this);
        itemDispensedState = new ItemDispensedState(this);
        currentState = noCoinState;
    }

    public void setState(State state) {
        this.currentState = state;
    }

    public State getNoCoinState() {
        return noCoinState;
    }

    public State getHasCoinState() {
        return hasCoinState;
    }

    public State getItemDispensedState() {
        return itemDispensedState;
    }

    public void insertCoin() {
        currentState.insertCoin();
    }

    public void selectItem() {
        currentState.selectItem();
    }

    public void dispense() {
        currentState.dispense();
    }
}
```

---

### 6. `Main` Method to Test

```java
public class Main {
    public static void main(String[] args) {
        VendingMachine machine = new VendingMachine();

        machine.insertCoin();
        machine.selectItem();
        machine.dispense();

        machine.insertCoin();
        machine.dispense(); // Invalid action order
    }
}
```

---

## 🧾 Output

```
Coin inserted.
Item selected.
Item dispensed.
Coin inserted.
Select item first.
```

---

## 🧼 Summary

| Concept        | State Pattern     |
|----------------|------------------|
| Code Structure | Cleaner           |
| Behavior       | Varies by State   |
| Flexibility    | Easy to Add States|
| Use Case       | Vending Machine   |