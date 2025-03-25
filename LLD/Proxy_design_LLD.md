# Proxy Design Pattern

## Overview
The **Proxy Design Pattern** is a structural design pattern that provides an object that acts as a substitute or placeholder for another object. The proxy controls access to the original object, allowing additional functionalities such as access control, logging, lazy initialization, and security.

## Intent
- Control access to an object.
- Implement lazy initialization.
- Add additional functionalities like logging and caching without modifying the original object.
- Protect sensitive objects from unauthorized access.

## Structure
The Proxy Design Pattern consists of the following components:
1. **Subject (Interface or Abstract Class)**: Defines the common interface for both RealSubject and Proxy.
2. **RealSubject (Actual Implementation)**: Represents the real object that performs operations.
3. **Proxy (Surrogate or Placeholder)**: Controls access to the RealSubject, implementing additional functionalities if necessary.

## UML Diagram
```
+------------------+
|    Subject      |
|----------------|
| +request()     |
+----------------+
        ^
        |
+------------------+       +------------------+
|     Proxy       |------>|  RealSubject     |
|----------------|       |----------------|
| +request()     |       | +request()     |
+----------------+       +----------------+
```

## Types of Proxy
1. **Virtual Proxy**: Used for lazy initialization and optimizing resource usage.
2. **Protection Proxy**: Controls access based on permissions.
3. **Remote Proxy**: Acts as a local representation of an object in a different address space.
4. **Cache Proxy**: Stores the results of expensive operations and returns cached results.
5. **Smart Proxy**: Adds additional behavior, like reference counting, before accessing the real object.

## Implementation in Java
```java
// Step 1: Define the Subject Interface
interface Image {
    void display();
}

// Step 2: RealSubject Implementation
class RealImage implements Image {
    private String fileName;

    public RealImage(String fileName) {
        this.fileName = fileName;
        loadFromDisk();
    }

    private void loadFromDisk() {
        System.out.println("Loading image: " + fileName);
    }

    @Override
    public void display() {
        System.out.println("Displaying image: " + fileName);
    }
}

// Step 3: Proxy Implementation
class ProxyImage implements Image {
    private RealImage realImage;
    private String fileName;

    public ProxyImage(String fileName) {
        this.fileName = fileName;
    }

    @Override
    public void display() {
        if (realImage == null) {
            realImage = new RealImage(fileName);
        }
        realImage.display();
    }
}

// Step 4: Client Code
public class ProxyPatternDemo {
    public static void main(String[] args) {
        Image image = new ProxyImage("test_image.jpg");
        
        // Image will be loaded from disk only once
        image.display();
        System.out.println("\nCalling display again:\n");
        image.display();
    }
}
```

## Output
```
Loading image: test_image.jpg
Displaying image: test_image.jpg

Calling display again:
Displaying image: test_image.jpg
```

## Use Cases
- **Security**: Control access to sensitive resources.
- **Performance Optimization**: Avoid expensive object creation.
- **Remote Proxy**: Represent an object located on a remote server.
- **Logging & Monitoring**: Track requests to an object.
- **Lazy Initialization**: Load objects only when needed.

## Advantages
- Reduces memory and resource usage by delaying object creation.
- Provides an additional layer of security.
- Enhances logging and access control.

## Disadvantages
- Adds complexity to the system.
- May introduce slight performance overhead.

## Conclusion
The **Proxy Design Pattern** is a powerful technique for controlling access, optimizing resource usage, and adding additional functionality to objects. It is widely used in various scenarios such as security, caching, and remote method invocation.

