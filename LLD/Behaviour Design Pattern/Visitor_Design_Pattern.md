## 🧠 Definition – What is the Visitor Design Pattern?

The **Visitor Design Pattern** allows you to **add new operations** to existing object structures **without modifying their classes**. It **separates** the logic of the operation from the object structure itself.

👉 Think of it as "**double dispatch**" – one dispatch based on the object, and another based on the visitor.

> 💡 It’s a **behavioral pattern** used when you have a hierarchy of elements (objects), and you want to perform new operations on them without altering their code.

---

## 🏨 Real-Life Analogy – Hotel Management System

Imagine a **hotel** with different types of **rooms**:

* 🛏️ Standard Room
* 👑 Deluxe Room
* 🧼 Suite Room

Now, various **visitors** (or staff) come to perform different operations:

* 🧹 **Housekeeping** – Cleans rooms
* 🧾 **Auditor** – Calculates revenue per room type
* 🛠️ **Maintenance Staff** – Checks electricals and water in each room

All these operations **should not be part of the room classes**. Instead, the room should simply **accept** a visitor and let it perform its task.

---

## 💻 Code Example – Java Visitor Design Pattern

Let’s break it down into:

1. `Room` interface
2. Concrete Room classes: `StandardRoom`, `DeluxeRoom`, `SuiteRoom`
3. `RoomVisitor` interface
4. Concrete visitors: `HousekeepingVisitor`, `AuditVisitor`

```java
// Step 1: The Element
interface Room {
    void accept(RoomVisitor visitor);
}

// Step 2: Concrete Elements
class StandardRoom implements Room {
    public void accept(RoomVisitor visitor) {
        visitor.visit(this);
    }

    public String getRoomType() {
        return "Standard Room";
    }
}

class DeluxeRoom implements Room {
    public void accept(RoomVisitor visitor) {
        visitor.visit(this);
    }

    public String getRoomType() {
        return "Deluxe Room";
    }
}

class SuiteRoom implements Room {
    public void accept(RoomVisitor visitor) {
        visitor.visit(this);
    }

    public String getRoomType() {
        return "Suite Room";
    }
}

// Step 3: The Visitor Interface
interface RoomVisitor {
    void visit(StandardRoom room);
    void visit(DeluxeRoom room);
    void visit(SuiteRoom room);
}

// Step 4: Concrete Visitors
class HousekeepingVisitor implements RoomVisitor {
    public void visit(StandardRoom room) {
        System.out.println("Cleaning " + room.getRoomType());
    }

    public void visit(DeluxeRoom room) {
        System.out.println("Cleaning " + room.getRoomType());
    }

    public void visit(SuiteRoom room) {
        System.out.println("Cleaning " + room.getRoomType());
    }
}

class AuditVisitor implements RoomVisitor {
    public void visit(StandardRoom room) {
        System.out.println("Auditing revenue for " + room.getRoomType());
    }

    public void visit(DeluxeRoom room) {
        System.out.println("Auditing revenue for " + room.getRoomType());
    }

    public void visit(SuiteRoom room) {
        System.out.println("Auditing revenue for " + room.getRoomType());
    }
}

// Step 5: Client Code
public class Hotel {
    public static void main(String[] args) {
        Room[] rooms = {
            new StandardRoom(),
            new DeluxeRoom(),
            new SuiteRoom()
        };

        RoomVisitor housekeeping = new HousekeepingVisitor();
        RoomVisitor auditor = new AuditVisitor();

        System.out.println("=== Housekeeping Visit ===");
        for (Room room : rooms) {
            room.accept(housekeeping);
        }

        System.out.println("\n=== Auditor Visit ===");
        for (Room room : rooms) {
            room.accept(auditor);
        }
    }
}
```

---

## ❓ Interview Insights – How to Explain It Concisely?

In interviews, **Visitor Pattern** is often asked in **System Design or LLD** rounds when:

* You need to apply **different operations** on elements of a structure
* You want to avoid **modifying** the class hierarchy

### 🧠 Elevator Pitch (1-2 lines):

> "Visitor Pattern lets you define new operations on a set of objects without changing their classes, by moving the operation logic to a visitor class and using double dispatch."

### 📦 Sample Interview Use Case:

**"Design a Tax Calculation system where different product categories (electronics, groceries, clothing) have different tax rules, and new rules may get added later."**

---

## ✅ Key Benefits

| Benefit                    | Explanation                                                   |
| -------------------------- | ------------------------------------------------------------- |
| **Open/Closed Principle**  | You can add new behaviors without changing existing classes   |
| **Separation of Concerns** | Business logic is separated from data structure               |
| **Double Dispatch**        | Behavior is determined based on both object and visitor types |
| **Easier Testing**         | Visitors can be tested independently                          |

---

## 📌 Best Practices – When and How to Use It Effectively

### ✅ Use When:

* Your object structure is **stable** but operations **change frequently**
* You want to **avoid polluting** classes with unrelated behaviors
* You need to **perform many unrelated operations** across class hierarchies

### 🚫 Avoid When:

* The object structure **changes often** – you'd have to update all visitors every time
* The logic **doesn’t justify** extra classes/complexity

### 🔧 Tips:

* Keep visitors **small and focused**
* Document the **contract between visitors and elements**
* If using with **inheritance**, ensure type-safe `visit()` methods

---

## 🎯 Summary

* Visitor Pattern = **Operation Extensibility**
* Real-World Analogy = **Visitors in a Hotel**
* Interview Focus = **Double Dispatch**, **Extensibility**
* Avoid When = **Object structure changes frequently**
* Ideal For = **Tax systems, compiler AST traversal, reporting, auditing**
