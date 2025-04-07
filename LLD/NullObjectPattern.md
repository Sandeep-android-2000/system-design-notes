# Null Object Design Pattern

## 🧠 What is it?

The **Null Object Design Pattern** is a behavioral design pattern that helps avoid `null` checks in code by using a default object that does nothing instead of returning `null`.

Instead of returning `null` and then checking `if (obj != null)`, we return a special object that behaves like a “do-nothing” version of the real object.

---

## 🛠️ When to Use

- You want to avoid `NullPointerExceptions`.
- You want to simplify conditional logic.
- You have many `if (object == null)` checks in your code.

---

## ✅ Benefits

- No more null checks.
- Cleaner, more readable code.
- Encourages use of polymorphism.

---

## 🚫 Without Null Object Pattern

```java
public class Main {
    public static void main(String[] args) {
        Customer customer = getCustomer("John");

        if (customer != null) {
            System.out.println("Customer name: " + customer.getName());
        } else {
            System.out.println("Customer not found.");
        }
    }

    public static Customer getCustomer(String name) {
        // pretend we searched in DB
        return null;
    }
}
```

---

## ✅ With Null Object Pattern

### 1. Step-by-Step Implementation

#### 👤 Abstract Class or Interface

```java
public abstract class Customer {
    public abstract String getName();
    public abstract boolean isNull();
}
```

#### 👤 RealCustomer

```java
public class RealCustomer extends Customer {
    private String name;

    public RealCustomer(String name) {
        this.name = name;
    }

    @Override
    public String getName() {
        return name;
    }

    @Override
    public boolean isNull() {
        return false;
    }
}
```

#### 🚫 NullCustomer

```java
public class NullCustomer extends Customer {

    @Override
    public String getName() {
        return "Not Available";
    }

    @Override
    public boolean isNull() {
        return true;
    }
}
```

#### 📁 CustomerFactory

```java
public class CustomerFactory {
    private static final String[] names = {"Alice", "Bob", "Charlie"};

    public static Customer getCustomer(String name) {
        for (String n : names) {
            if (n.equalsIgnoreCase(name)) {
                return new RealCustomer(name);
            }
        }
        return new NullCustomer();
    }
}
```

#### ▶️ Main Class (Usage)

```java
public class Main {
    public static void main(String[] args) {
        Customer customer1 = CustomerFactory.getCustomer("Alice");
        Customer customer2 = CustomerFactory.getCustomer("Xyz");

        System.out.println("Customer1: " + customer1.getName());
        System.out.println("Customer2: " + customer2.getName());
    }
}
```

---

## 🧾 Output

```
Customer1: Alice
Customer2: Not Available
```

---

## 🧼 Summary

| Concept        | With Null Object |
|----------------|------------------|
| Null Checks    | ❌ No            |
| Polymorphism   | ✅ Yes           |
| Readability    | ✅ Cleaner code  |
| Errors         | ❌ No NullPointerException |