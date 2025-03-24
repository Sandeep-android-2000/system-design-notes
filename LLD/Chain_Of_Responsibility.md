# Chain of Responsibility Design Pattern

## Overview
The **Chain of Responsibility** is a behavioral design pattern that allows multiple objects to handle a request sequentially. This pattern decouples the sender of the request from its receiver by passing the request along a chain of handlers until one of them processes it.

## Key Concepts
- **Handler**: An interface or abstract class defining a method to process a request and a reference to the next handler.
- **Concrete Handlers**: Specific classes that implement the handler interface, deciding whether to process the request or pass it to the next handler.
- **Client**: The entity that initiates the request and sends it to the first handler.
- **Chain of Handlers**: A linked structure where each handler is responsible for processing or forwarding the request.

## Structure
```
Client → Handler 1 → Handler 2 → Handler 3 → ...
```
Each handler either processes the request or forwards it to the next handler in the chain.

## Advantages
- **Decouples sender and receiver**: The client doesn’t need to know which handler will process the request.
- **Flexibility in assigning responsibilities**: Handlers can be dynamically added or removed from the chain.
- **Promotes Single Responsibility Principle (SRP)**: Each handler is focused on a specific aspect of request processing.

## Disadvantages
- **Request might not be handled**: If no handler processes the request, it could go unhandled.
- **Debugging can be difficult**: Tracing the request through multiple handlers can be complex.

## Implementation in Java (Logger Example)
```java
// Step 1: Define an abstract logger
abstract class Logger {
    protected Logger nextLogger;
    
    public void setNextLogger(Logger nextLogger) {
        this.nextLogger = nextLogger;
    }
    
    public void logMessage(int level, String message) {
        if (canHandle(level)) {
            write(message);
        } else if (nextLogger != null) {
            nextLogger.logMessage(level, message);
        }
    }
    
    protected abstract boolean canHandle(int level);
    protected abstract void write(String message);
}

// Step 2: Create concrete loggers
class InfoLogger extends Logger {
    protected boolean canHandle(int level) {
        return level == 1;
    }
    
    protected void write(String message) {
        System.out.println("INFO: " + message);
    }
}

class DebugLogger extends Logger {
    protected boolean canHandle(int level) {
        return level == 2;
    }
    
    protected void write(String message) {
        System.out.println("DEBUG: " + message);
    }
}

class ErrorLogger extends Logger {
    protected boolean canHandle(int level) {
        return level == 3;
    }
    
    protected void write(String message) {
        System.out.println("ERROR: " + message);
    }
}

// Step 3: Client usage
public class ChainOfResponsibilityExample {
    public static void main(String[] args) {
        Logger errorLogger = new ErrorLogger();
        Logger debugLogger = new DebugLogger();
        Logger infoLogger = new InfoLogger();
        
        infoLogger.setNextLogger(debugLogger);
        debugLogger.setNextLogger(errorLogger);
        
        infoLogger.logMessage(1, "This is an info message"); // Handled by InfoLogger
        infoLogger.logMessage(2, "This is a debug message"); // Passed to DebugLogger
        infoLogger.logMessage(3, "This is an error message"); // Passed to ErrorLogger
    }
}
```

## Use Cases
- **Logging frameworks**: Logs are passed through multiple handlers (console, file, remote server, etc.).
- **Authentication and Authorization**: Requests pass through different security layers.
- **UI Event Handling**: Events are passed through a chain of listeners.

## Conclusion
The **Chain of Responsibility** pattern is useful for scenarios where multiple objects can handle a request dynamically. It enhances code maintainability by reducing direct dependencies between objects.
