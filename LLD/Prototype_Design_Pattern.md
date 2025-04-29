
# 🧬 Prototype Design Pattern (Creational Pattern)

## 📌 Definition

The **Prototype Design Pattern** is a creational pattern used to create **duplicate objects** while keeping performance in mind. Instead of creating a new instance from scratch, it allows copying an existing object (the *prototype*) to produce new objects.

---

## ✅ When to Use

- When object creation is **costly** (e.g., time-consuming, resource-intensive).
- When you need to create **many similar objects**.
- When creating an object involves **complex setup/configuration**.
- When you want to **avoid subclassing** just for object creation.

---

## 🛠️ How It Works

1. You define a **Prototype interface** with a `clone()` method.
2. Concrete classes implement this interface and provide their own `clone()` logic.
3. When you need a new object, you **clone an existing prototype** instead of creating a new one from scratch.

---

## 👨‍💻 Code Example (Java)

```java
// Step 1: Prototype interface
public interface PlantPrototype {
    PlantPrototype clone();  // The clone method
}

// Step 2: Concrete class implementing the prototype
public class MedicinalPlant implements PlantPrototype {
    private String name;
    private String use;

    public MedicinalPlant(String name, String use) {
        this.name = name;
        this.use = use;
    }

    @Override
    public PlantPrototype clone() {
        return new MedicinalPlant(name, use); // shallow copy
    }

    public void display() {
        System.out.println(name + " used for " + use);
    }
}

// Step 3: Using the prototype
public class Garden {
    public static void main(String[] args) {
        MedicinalPlant tulsi = new MedicinalPlant("Tulsi", "Cough and Cold");

        // Clone instead of creating a new object
        MedicinalPlant clonedTulsi = (MedicinalPlant) tulsi.clone();

        tulsi.display();
        clonedTulsi.display();
    }
}
```

---

## 🎯 Advantages

- Saves memory and time when creating expensive objects.
- Avoids the need for subclasses just for object creation.
- Simplifies object creation logic.

---

## ⚠️ Disadvantages

- Deep cloning (when objects have references to other objects) can be complex.
- Maintaining a clone method for each new subclass may be tedious.

---

## 🧠 Real-Life Analogy

Think of a **document template**. Instead of writing a new document from scratch every time, you make a copy of a template and customize it. The template acts as a prototype.
