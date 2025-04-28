
# Object Pool Design Pattern (Beginner-Friendly)

## 🧠 What is Object Pool Design Pattern?

**Object Pool** is a **creational design pattern** that helps you **reuse objects** instead of creating and destroying them again and again.

- **Why?**  
  Creating an object (especially heavy ones like database connections, thread objects, etc.) can be slow and costly.
- **How?**  
  Instead of creating a new object every time, you **borrow** an existing object from a **pool** (a collection).  
  When you're done, you **return** it to the pool for others to reuse.

> Think of it like a **library**: Instead of buying a new book every time, you **borrow** a book, read it, and **return** it when done.

---

## 🎯 Where is Object Pool Used?
- Database connections
- Thread pools (like ExecutorService in Java)
- Game development (e.g., bullets, enemies, etc.)
- Network socket connections

---

## 🛠 How Does It Work?

1. **Check the Pool**: Look for an available object in the pool.
2. **Borrow or Create**: If available, borrow it. If not, create a new one (if the pool isn't full).
3. **Use the Object**: Do your task using the object.
4. **Return the Object**: Return it to the pool when done.

---

## 📝 Simple Example (Java-Like Pseudocode)

```java
class ObjectPool {
    List<ReusableObject> available = new ArrayList<>();

    ReusableObject getObject() {
        if (available.isEmpty()) {
            return new ReusableObject(); // Create a new one
        } else {
            return available.remove(0); // Reuse from pool
        }
    }

    void returnObject(ReusableObject obj) {
        available.add(obj); // Return to pool
    }
}

class ReusableObject {
    // Some heavy object
}
```

**Usage:**

```java
ObjectPool pool = new ObjectPool();
ReusableObject obj1 = pool.getObject();

// use obj1

pool.returnObject(obj1); // Reuse it later
```

---

## 🚀 Advanced Example: Database Connection Pool with Singleton and Thread-Safety

When dealing with database connections, object pooling becomes extremely useful.

Here’s an **optimized** version:

```java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;
import java.util.concurrent.BlockingQueue;
import java.util.concurrent.LinkedBlockingQueue;

public class DBConnectionPool {

    private static DBConnectionPool instance;
    private static final int MAX_POOL_SIZE = 5;
    private BlockingQueue<Connection> connectionPool;

    private DBConnectionPool() {
        connectionPool = new LinkedBlockingQueue<>(MAX_POOL_SIZE);
        initializeConnections();
    }

    // Singleton Instance (Thread Safe with synchronized block)
    public static DBConnectionPool getInstance() {
        if (instance == null) {
            synchronized (DBConnectionPool.class) {
                if (instance == null) {
                    instance = new DBConnectionPool();
                }
            }
        }
        return instance;
    }

    private void initializeConnections() {
        try {
            for (int i = 0; i < MAX_POOL_SIZE; i++) {
                Connection conn = DriverManager.getConnection(
                    "jdbc:mysql://localhost:3306/yourdb", "username", "password");
                connectionPool.offer(conn);
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }

    public Connection borrowConnection() throws InterruptedException {
        return connectionPool.take(); // waits if pool is empty
    }

    public void returnConnection(Connection connection) {
        if (connection != null) {
            connectionPool.offer(connection);
        }
    }
}
```

**Usage:**

```java
public class App {
    public static void main(String[] args) {
        try {
            DBConnectionPool pool = DBConnectionPool.getInstance();
            Connection conn = pool.borrowConnection();

            // Use the connection (execute queries)

            pool.returnConnection(conn); // Return it back to the pool
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

---

## ⚡ Advantages
- Faster performance (no repeated creation/destruction)
- Saves memory
- Singleton ensures one pool for the entire application
- Thread-safe (multiple threads can safely borrow/return objects)

## ⚠️ Disadvantages
- Managing the pool correctly can be tricky
- Risk of stale or invalid objects if not reset properly

---
