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
1. **Access Restriction**: Proxies can be used to control access to sensitive objects, ensuring that only authorized users can interact with them.
2. **Caching**: A proxy can store expensive computations or database results to return precomputed values instead of recalculating them.
3. **Preprocessing and Postprocessing**: Proxies can add extra logic before and after method calls, such as logging, performance monitoring, or security checks.

### Real-life Use Case in Spring Boot
One practical use of the Proxy Design Pattern in Spring Boot is during the **bean creation process**. When creating beans, Spring uses proxies (like CGLIB or JDK dynamic proxies) to enable features such as:
- **Transaction management**: Spring wraps beans with proxies to manage transactions seamlessly.
- **Lazy initialization**: Proxies allow the deferred instantiation of beans.
- **AOP (Aspect-Oriented Programming)**: Proxies enable method interception for cross-cutting concerns like logging and security.

Example:
```java
@Component
public class MyService {
    public void performOperation() {
        System.out.println("Performing operation");
    }
}

@Configuration
@EnableAspectJAutoProxy
class AppConfig {
}

@Aspect
@Component
class LoggingAspect {
    @Before("execution(* MyService.performOperation(..))")
    public void logBefore() {
        System.out.println("Logging before method execution");
    }
}
```
Here, **Spring AOP creates a proxy around `MyService`** to intercept method calls and add logging before execution.

## Advantages
- Reduces memory and resource usage by delaying object creation.
- Provides an additional layer of security.
- Enhances logging and access control.
- Supports performance optimizations like caching.

## Disadvantages
- Adds complexity to the system.
- May introduce slight performance overhead.

## Conclusion
The **Proxy Design Pattern** is a powerful technique for controlling access, optimizing resource usage, and adding additional functionality to objects. It is widely used in various scenarios such as security, caching, and remote method invocation. In Spring Boot, proxies are essential for managing transactions, AOP, and lazy initialization.

