
# 🔁 Iterator Design Pattern

## 📘 What is it?

The **Iterator Pattern** is a **behavioral design pattern** that allows sequential access to elements of a collection (like List, Set, or Array) **without exposing its underlying structure**.

> It’s like having a TV remote to go through channels one by one without knowing how the channels are stored internally.

---

## 🧠 Type of Design Pattern

- **Category**: Behavioral Pattern
- **Purpose**: Access elements of a collection in a uniform way, **without knowing** its internal details.

---

## 📊 UML Diagram

```
      +----------------+       +----------------------+
      |  Iterable      |<>---->|     Iterator         |
      +----------------+       +----------------------+
      | +createIterator()      | +hasNext(): boolean  |
      |                        | +next(): Object      |
      +----------------+       +----------------------+
             ^                          ^
             |                          |
   +----------------+          +----------------------+
   | ConcreteCollection|        |  ConcreteIterator    |
   +----------------+          +----------------------+
   | +items          |          | +position: int       |
   | +createIterator()|         | +hasNext(), next()   |
   +----------------+          +----------------------+
```

---

## 🎯 Real World Example

Imagine a **music playlist app**. You don’t care how songs are stored (array, DB, etc.), you just want to:
- Play next song
- Know if there are more songs left

The iterator does this job for you.

---

## 💻 Java Code Example

### 1. `Iterator` interface:
```java
public interface Iterator {
    boolean hasNext();
    Object next();
}
```

### 2. `Container` interface (collection):
```java
public interface Container {
    Iterator getIterator();
}
```

### 3. `NameRepository` class:
```java
public class NameRepository implements Container {

    public String[] names = {"John", "Jane", "Alice", "Bob"};

    @Override
    public Iterator getIterator() {
        return new NameIterator();
    }

    private class NameIterator implements Iterator {
        int index = 0;

        @Override
        public boolean hasNext() {
            return index < names.length;
        }

        @Override
        public Object next() {
            if (this.hasNext()) {
                return names[index++];
            }
            return null;
        }
    }
}
```

### 4. Usage Example:
```java
public class IteratorPatternDemo {
    public static void main(String[] args) {
        NameRepository repo = new NameRepository();

        for (Iterator iter = repo.getIterator(); iter.hasNext(); ) {
            String name = (String) iter.next();
            System.out.println("Name: " + name);
        }
    }
}
```

---

## ✅ Output

```
Name: John
Name: Jane
Name: Alice
Name: Bob
```

---

## 🧾 Summary

| Feature              | Description                                       |
|----------------------|---------------------------------------------------|
| Type                 | Behavioral                                         |
| Purpose              | Sequential access to collection elements          |
| Real-world Example   | TV Remote, Playlist navigation                    |
| Benefit              | Don't need to know the internal data structure    |
