
# 🧾 Memento (Snapshot) Design Pattern

## 📘 Definition

The **Memento Pattern** captures and externalizes an object’s internal state so that it can be **restored later**—without violating encapsulation.

---

## 🎯 Purpose

- To implement **undo/rollback** functionality.
- Saves the **state of an object** so it can be restored when needed.

---

## 📦 Real-World Analogy

Imagine you’re **writing a document in a word processor**. Every time you hit "Save" or "Undo", the editor **restores a previous state**—that’s the Memento Pattern in action!

---

## 🧠 Key Components

| Role       | Description |
|------------|-------------|
| **Originator** | The object whose state needs to be saved. |
| **Memento**    | Stores the internal state of the originator. |
| **Caretaker**  | Keeps track of the memento(s) but never modifies them. |

---

## 🛠️ Structure (Java Example)

### 1. Memento Class

```java
public class Memento {
    private final String state;

    public Memento(String state) {
        this.state = state;
    }

    public String getState() {
        return state;
    }
}
```

### 2. Originator Class

```java
public class Originator {
    private String state;

    public void setState(String state) {
        this.state = state;
        System.out.println("State set to: " + state);
    }

    public String getState() {
        return state;
    }

    public Memento saveStateToMemento() {
        return new Memento(state);
    }

    public void restoreStateFromMemento(Memento memento) {
        state = memento.getState();
        System.out.println("State restored to: " + state);
    }
}
```

### 3. Caretaker Class

```java
import java.util.*;

public class Caretaker {
    private List<Memento> mementoList = new ArrayList<>();

    public void addMemento(Memento m) {
        mementoList.add(m);
    }

    public Memento getMemento(int index) {
        return mementoList.get(index);
    }
}
```

---

## 🧪 Usage Example

```java
public class Main {
    public static void main(String[] args) {
        Originator originator = new Originator();
        Caretaker caretaker = new Caretaker();

        originator.setState("State #1");
        caretaker.addMemento(originator.saveStateToMemento());

        originator.setState("State #2");
        caretaker.addMemento(originator.saveStateToMemento());

        originator.setState("State #3");
        System.out.println("Current State: " + originator.getState());

        originator.restoreStateFromMemento(caretaker.getMemento(0)); // Restores to State #1
        originator.restoreStateFromMemento(caretaker.getMemento(1)); // Restores to State #2
    }
}
```

---

## ✅ Advantages

- Adds **undo** capabilities to applications.
- Respects **encapsulation**—internal details of an object aren't exposed.

---

## ❌ Disadvantages

- Can consume a **lot of memory** if many states are stored.
- Caretaker needs to **manage memento history**, which can become complex.

---

## 🧮 Use Cases

- Undo functionality in editors.
- State checkpoints in games.
- Version history systems.
