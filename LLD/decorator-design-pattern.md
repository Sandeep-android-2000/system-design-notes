# Decorator Design Pattern in System Design

## **What is the Decorator Pattern?**
The **Decorator Pattern** is a **structural design pattern** used to dynamically extend the behavior of objects without modifying their original code. It follows the principle of **composition over inheritance**, allowing flexibility in adding new functionality to objects at runtime.

## **Key Concepts:**
- **Component (Interface/Abstract Class):** Defines the common interface for both concrete components and decorators.
- **Concrete Component:** The base class that defines the core functionality.
- **Decorator:** A wrapper class that adds additional behavior while following the same interface.
- **Concrete Decorators:** Extend the functionality of the component by wrapping it.

---

# **Real-world System Design Example – Logging in Microservices**

In a **microservices architecture**, logging plays a crucial role in tracking requests and debugging issues. Using the **Decorator Pattern**, we can enhance logging functionality without modifying the core request-handling logic.

---

# **Implementation in Java**

## **Step 1: Create a Common Interface**
```java
interface Logger {
    void log(String message);
}
```

## **Step 2: Implement the Concrete Logger (Base Component)**
```java
class SimpleLogger implements Logger {
    @Override
    public void log(String message) {
        System.out.println("Log: " + message);
    }
}
```

## **Step 3: Create an Abstract Decorator**
```java
abstract class LoggerDecorator implements Logger {
    protected Logger logger;

    public LoggerDecorator(Logger logger) {
        this.logger = logger;
    }

    @Override
    public void log(String message) {
        logger.log(message);
    }
}
```

## **Step 4: Implement Concrete Decorators**

### **Adding Timestamp to Logs**
```java
import java.time.LocalDateTime;

class TimestampLogger extends LoggerDecorator {
    public TimestampLogger(Logger logger) {
        super(logger);
    }

    @Override
    public void log(String message) {
        super.log(LocalDateTime.now() + " - " + message);
    }
}
```

### **Adding Log Level (INFO, DEBUG, ERROR)**
```java
class LevelLogger extends LoggerDecorator {
    private String level;

    public LevelLogger(Logger logger, String level) {
        super(logger);
        this.level = level;
    }

    @Override
    public void log(String message) {
        super.log("[" + level + "] " + message);
    }
}
```

## **Step 5: Demonstrate the Decorator Pattern**
```java
public class DecoratorPatternDemo {
    public static void main(String[] args) {
        Logger simpleLogger = new SimpleLogger();
        Logger timestampLogger = new TimestampLogger(simpleLogger);
        Logger levelLogger = new LevelLogger(timestampLogger, "INFO");

        System.out.println("Basic Logging:");
        simpleLogger.log("User logged in.");

        System.out.println("\nLogging with Timestamp:");
        timestampLogger.log("User added item to cart.");

        System.out.println("\nLogging with Timestamp & Log Level:");
        levelLogger.log("Payment processed successfully.");
    }
}
```

---

## **Benefits of the Decorator Pattern in System Design**
✔ **Flexible:** Easily extends functionality without modifying existing code.  
✔ **Reusable Components:** Different decorators can be combined to create unique behaviors.  
✔ **Open/Closed Principle:** Allows extending behavior without altering existing code.  
✔ **Separation of Concerns:** Keeps core functionality separate from additional features.  

By implementing the **Decorator Pattern**, we create a **modular, extensible logging system** in a microservices architecture, making debugging and monitoring more efficient.
