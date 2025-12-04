# 🌳 Composite Design Pattern — Beginner-Friendly Documentation  
*Clear, intuitive explanation for beginners + interview-ready depth*

---

# 📌 1. What Is the Composite Design Pattern? (Simple Explanation)

Imagine you have a **Company Hierarchy**:

- CEO  
  - Manager  
    - Developer  
    - Developer  
  - HR Manager  
    - HR Executive  

Some employees are **single objects** (leaf nodes),  
and some are **containers** holding other employees (composite nodes).

But you want to treat **every employee the same way**:

- printDetails()  
- calculateSalary()  
- assignTask()  

That means:  
> Whether an object is simple (leaf) or complex (tree),  
> you interact with it **using the same interface**.

This is exactly what **Composite Pattern** does.

---

# 🎯 2. What Problem Does Composite Solve?

## ❌ Without Composite Pattern  
You end up writing:

```
if (object is a leaf)
    process leaf logic
else if (object is a composite)
    loop through children and process
```

This leads to:
- Many `instanceof` checks  
- Ugly if-else chains  
- Hard-to-maintain code  
- Violation of **Open/Closed Principle**  
- Tight coupling  

---

## ✔️ With Composite Pattern  
You define **one common interface**, and both:
- *Leaf objects*  
- *Composite objects*  

implement the same interface.

### As a result:
- Client code doesn’t care if it’s talking to a leaf or a tree.  
- Hierarchical structures (trees) become easy to manage.  
- Code is cleaner and follows SOLID (especially OCP & LSP).

---

# 🌍 3. Real-World Use Cases

### UI Components  
- Button (leaf)  
- Panel (composite)  
- Window (composite)  
All implement: `render()`

### File System  
- File (leaf)  
- Folder (composite)  
Both implement: `open()`, `size()`

### Product Bundles in E-commerce  
- Single item  
- Pack of items  
Both implement `getPrice()`

### XML/HTML Nodes  
- `<p>` leaf  
- `<div>` composite  
Both support: `draw()`, `addChild()`

### Organization Structure  
- Employee vs Manager  
Both support: `getDetails()`

---

# 🧠 4. Core Concepts

| Component | Purpose |
|----------|---------|
| **Component (Interface)** | Common interface for both leaf & composite |
| **Leaf** | Single object without children |
| **Composite** | Object that holds children & performs operations recursively |
| **Client** | Consumes objects uniformly regardless of type |

---

# 📐 5. Proper UML for Composite Design Pattern

```
                    +--------------------+
                    |    Component       |
                    +--------------------+
                    | + operation()      |
                    +---------+----------+
                              ^
                              |
          +-------------------+---------------------+
          |                                         |
+--------------------+                   +-------------------------+
|       Leaf         |                   |       Composite        |
+--------------------+                   +-------------------------+
| + operation()      |                   | + children: List<Component> |
|                    |                   | + add(Component)          |
+--------------------+                   | + remove(Component)       |
                                         | + operation()             |
                                         +---------------------------+
```

### ✔️ Client sees both **Leaf** and **Composite** as `Component`.

---

# 💻 6. Java Code Example (Very Clear)

## Step 1: Component Interface

```java
interface FileSystem {
    void showDetails();
}
```

---

## Step 2: Leaf Class

```java
class File implements FileSystem {

    private String name;

    public File(String name) {
        this.name = name;
    }

    @Override
    public void showDetails() {
        System.out.println("File: " + name);
    }
}
```

---

## Step 3: Composite Class

```java
import java.util.ArrayList;
import java.util.List;

class Folder implements FileSystem {

    private String name;
    private List<FileSystem> children = new ArrayList<>();

    public Folder(String name) {
        this.name = name;
    }

    public void add(FileSystem fs) {
        children.add(fs);
    }

    public void remove(FileSystem fs) {
        children.remove(fs);
    }

    @Override
    public void showDetails() {
        System.out.println("Folder: " + name);
        for (FileSystem fs : children) {
            fs.showDetails();
        }
    }
}
```

---

## Step 4: Client Code

```java
public class Main {
    public static void main(String[] args) {

        File file1 = new File("notes.txt");
        File file2 = new File("resume.pdf");

        Folder docs = new Folder("Documents");
        docs.add(file1);
        docs.add(file2);

        File img = new File("profile.png");
        Folder images = new Folder("Images");
        images.add(img);

        Folder root = new Folder("Root");
        root.add(docs);
        root.add(images);

        root.showDetails();
    }
}
```

### Output:

```
Folder: Root
Folder: Documents
File: notes.txt
File: resume.pdf
Folder: Images
File: profile.png
```

---

# 💡 7. How Composite Organizes the Code

Composite makes your architecture:

### ✔️ Clean  
No more `if (leaf)` or `if (folder)` checks.

### ✔️ Extensible  
Adding new leaf types doesn’t require touching existing code.

### ✔️ Uniform  
Client treats everything as `Component`.

### ✔️ Recursive  
Composite objects call `operation()` on child components.

---

# ✔️ Pros

- Treat individual and group objects the same  
- Easy to add new components  
- Eliminates complex conditionals  
- Great for tree-like data structures  

---

# ❌ Cons

- Harder to restrict what composites or leaves can contain  
- Might make design too generalized  

---

# ⚠️ When NOT to Use Composite

- When you do NOT have hierarchical/tree structures  
- When leaf and composite behaviors differ too much to share an interface  
- When strict control is required (e.g., a folder must only contain folders)

---

# 🏁 Final Summary

> Composite Pattern lets you treat **individual objects** and **collections of objects**  
> **uniformly using the same interface**.

It’s perfect for:
- File systems  
- UI trees  
- Organizational charts  
- Scene graphs  
- Product bundles  

---

