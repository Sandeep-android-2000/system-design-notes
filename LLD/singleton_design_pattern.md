
# 🧠 Singleton Design Pattern

## 📘 Definition

The **Singleton Pattern** ensures that a **class has only one instance** and provides a **global point of access** to that instance.

---

## 🎯 Purpose

- Prevents multiple instances of a class.
- Useful when **one object** is needed to coordinate actions across the system (e.g., database connections, config managers, loggers).

---

## 📦 Real-World Analogy

Imagine a **president** of a country. There should be **only one president** at a time. If someone tries to create another president, we return the already existing one.

---

## 🛠️ Structure

```java
public class Singleton {
    private static Singleton instance;

    private Singleton() {
        // private constructor prevents instantiation from outside
    }

    public static Singleton getInstance() {
        if (instance == null) {
            instance = new Singleton(); // creates instance if it doesn't exist
        }
        return instance;
    }
}
```

---

## 🧪 How to Use

```java
Singleton obj1 = Singleton.getInstance();
Singleton obj2 = Singleton.getInstance();

System.out.println(obj1 == obj2); // true
```

---

## 🧵 Types of Singleton in Java

### 1. **Eager Initialization**
- Instance is created at class loading time.
- Thread-safe without synchronization.
- Drawback: creates instance even if not used.

```java
public class Singleton {
    private static final Singleton instance = new Singleton();

    private Singleton() {}

    public static Singleton getInstance() {
        return instance;
    }
}
```

---

### 2. **Lazy Initialization**
- Instance created only when requested.
- Not thread-safe.

```java
public class Singleton {
    private static Singleton instance;

    private Singleton() {}

    public static Singleton getInstance() {
        if (instance == null) {
            instance = new Singleton();
        }
        return instance;
    }
}
```

---

### 3. **Thread-Safe Singleton**
- Ensures only one thread can create the instance.
- Uses `synchronized`.

```java
public class Singleton {
    private static Singleton instance;

    private Singleton() {}

    public static synchronized Singleton getInstance() {
        if (instance == null) {
            instance = new Singleton();
        }
        return instance;
    }
}
```

---

### 4. **Double-Checked Locking (Recommended)**
- Improves performance by checking twice before creating instance.

```java
public class Singleton {
    private static volatile Singleton instance;

    private Singleton() {}

    public static Singleton getInstance() {
        if (instance == null) {
            synchronized (Singleton.class) {
                if (instance == null) {
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
}
```

---

### 5. **Bill Pugh Singleton (Best Practice)**
- Uses static inner class.
- Thread-safe and lazy without synchronization overhead.

```java
public class Singleton {
    private Singleton() {}

    private static class Holder {
        private static final Singleton INSTANCE = new Singleton();
    }

    public static Singleton getInstance() {
        return Holder.INSTANCE;
    }
}
```

---

## ✅ Advantages

- Controlled access to a single instance.
- Saves memory.
- Useful for shared resources (e.g., DB connections).

---

## ❌ Disadvantages

- Difficult to unit test (due to global state).
- Can hide dependencies.
- Might violate Single Responsibility Principle.
