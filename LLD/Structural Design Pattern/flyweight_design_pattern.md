# 🪶 Flyweight Design Pattern — Beginner-Friendly Documentation  
*Memory‑efficient structural pattern explained in simple language*

---

# 📌 1. What Is the Flyweight Pattern? (Simple Explanation)

Imagine a game where **10,00,000 trees** appear on screen.  
Each tree has:

- x, y coordinates  
- texture image (PNG)  
- color  
- size  
- type  

If every tree stores its **own** texture, color, etc., memory usage explodes.

### ✔️ Flyweight Pattern FIXES THIS  
Flyweight says:

> “Store **shared**, **heavy**, **intrinsic** data in a common object and reuse it.  
> Only store unique, lightweight data separately.”

So instead of **1 million full tree objects**,  
you have:

- Few shared **TreeType (flyweight)** objects  
- Many **Tree (context)** objects holding only x, y coordinates

---

# 🎯 2. What Problem Does Flyweight Solve?

## ❌ Without Flyweight  
Each object contains:
- Large texture data  
- Colors  
- Fonts  
- Configurations  

Millions of objects → **RAM explodes** → app becomes slow.

### Problems:
- Redundant duplicate data  
- High memory usage  
- Poor performance  
- Cannot scale to large object counts  

---

## ✔️ With Flyweight  
We split object data into:

### **Intrinsic State (shared part)**  
- Constant data  
- Does NOT change per object  
- Stored in Flyweight object

### **Extrinsic State (unique part)**  
- Provided by the client at runtime  
- Not stored inside the Flyweight  

This reduces memory usage dramatically.

---

# 🌍 3. Real-World Use Cases

### 🎮 Game Development  
- Trees, rocks, grass  
- Enemies of same type  
- Bullet textures  

### 🖼 Graphics Applications  
- Repeated icons  
- Font glyphs  
- Character objects in text rendering  

### 📝 Text Editors (MOST FAMOUS EXAMPLE)  
Each character on screen is a flyweight:
- Intrinsic: font, style  
- Extrinsic: position (x, y)

This allows rendering millions of characters efficiently.

### 🌐 Web Browsers  
- Tab UI components  
- Rendering repeated DOM elements  

### ⚙️ Java Internal Examples  
- `Integer.valueOf()` caches -128 to 127  
- String Pool (`"abc" == "abc"` → true)  
- Boolean and Byte caching  

---

# 🧠 4. Core Concepts

| Concept | Description |
|--------|-------------|
| **Flyweight** | Shared, reusable object with intrinsic state |
| **Intrinsic State** | Heavy, constant data stored in flyweight |
| **Extrinsic State** | Passed from client; unique per object |
| **FlyweightFactory** | Ensures reuse; returns existing flyweights |
| **Client** | Uses flyweights and supplies extrinsic data |

---

# 📐 5. Correct UML Diagram for Flyweight Pattern

```
                          +------------------------+
                          |      Flyweight         |
                          +------------------------+
                          | + operation(state)     |
                          +-----------+------------+
                                      ^
                                      |
                         +------------+-------------+
                         |                          |
             +-----------------------+   +-----------------------+
             |  ConcreteFlyweight    |   |  UnsharedFlyweight    |
             +-----------------------+   +-----------------------+
             | intrinsicState        |   |  (rarely used)        |
             +-----------------------+   +-----------------------+
                         ^
                         |
           +-------------+--------------+
           |      FlyweightFactory     |
           +----------------------------+
           | - pool: Map<Key,Flyweight> |
           | + getFlyweight(key)        |
           +-------------+--------------+
                         |
                         v
                    +---------+
                    | Client  |
                    +---------+
                    | extrinsicState |
                    +----------------+
```

---

# 💻 6. Java Code Example (Easy & Clear)

## Step 1: Flyweight Class (shared data)

```java
class TreeType {
    private String name;
    private String color;
    private String texture; // heavy object (path or bytes)

    public TreeType(String name, String color, String texture) {
        this.name = name;
        this.color = color;
        this.texture = texture;
    }

    public void draw(int x, int y) {
        System.out.println("Drawing " + name + " tree at (" + x + "," + y + ")");
    }
}
```

---

## Step 2: Flyweight Factory

```java
import java.util.HashMap;

class TreeFactory {

    private static final HashMap<String, TreeType> treeTypes = new HashMap<>();

    public static TreeType getTreeType(String name, String color, String texture) {

        String key = name + color + texture;

        if (!treeTypes.containsKey(key)) {
            treeTypes.put(key, new TreeType(name, color, texture));
        }

        return treeTypes.get(key);
    }
}
```

---

## Step 3: Context Object (stores extrinsic state)

```java
class Tree {

    private int x;
    private int y;
    private TreeType type; // shared flyweight

    public Tree(int x, int y, TreeType type) {
        this.x = x;
        this.y = y;
        this.type = type;
    }

    public void draw() {
        type.draw(x, y); // pass extrinsic state
    }
}
```

---

## Step 4: Client Code

```java
public class Main {

    public static void main(String[] args) {

        TreeType oakType = TreeFactory.getTreeType("Oak", "Green", "oak_texture.png");

        Tree tree1 = new Tree(10, 20, oakType);
        Tree tree2 = new Tree(50, 80, oakType);
        Tree tree3 = new Tree(100, 200, oakType);

        tree1.draw();
        tree2.draw();
        tree3.draw();
    }
}
```

---

# 💡 7. How Flyweight Organizes the Code

### ✔️ Centralizes heavy shared data  
Large assets (textures, objects, configs) live in **one place**.

### ✔️ De-duplicates memory  
Instead of storing identical data millions of times, you reuse one instance.

### ✔️ Makes object creation extremely cheap  
You create only the lightweight wrapper (context), not the heavy data.

### ✔️ Improves performance  
Perfect for:
- Games  
- Big rendering engines  
- Cloud-scale microservices with repeated objects  

---

# ✔️ Pros

- Huge memory savings  
- Faster performance with millions of objects  
- Clean separation of states  
- Efficient reuse of heavy resources  

---

# ❌ Cons

- Logic becomes slightly complex  
- Extrinsic state management must be handled carefully  
- Harder to use when objects differ too much  

---

# ⚠️ When NOT to Use Flyweight

- When objects do NOT share common heavy data  
- When memory is not a problem  
- When extrinsic/intrinsic split becomes messy  
- When objects change frequently (breaks immutability)

---

# 🏁 Final Summary

> Flyweight Pattern enables creating **millions** of lightweight objects  
> by sharing **heavy intrinsic data** and keeping only **unique extrinsic data** outside.

Perfect for:
- Games  
- GUI rendering  
- Text processing  
- Data visualization  
- High-performance systems  

---

