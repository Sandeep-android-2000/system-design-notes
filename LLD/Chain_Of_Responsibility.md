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

## Implementation in Java
```java
// Step 1: Define an abstract handler
abstract class Handler {
    protected Handler nextHandler;
    
    public void setNextHandler(Handler nextHandler) {
        this.nextHandler = nextHandler;
    }
    
    public abstract void handleRequest(String request);
}

// Step 2: Create concrete handlers
class ConcreteHandlerA extends Handler {
    public void handleRequest(String request) {
        if (request.equals("A")) {
            System.out.println("Handler A processed request");
        } else if (nextHandler != null) {
            nextHandler.handleRequest(request);
        }
    }
}

class ConcreteHandlerB extends Handler {
    public void handleRequest(String request) {
        if (request.equals("B")) {
            System.out.println("Handler B processed request");
        } else if (nextHandler != null) {
            nextHandler.handleRequest(request);
        }
    }
}

// Step 3: Client usage
public class ChainOfResponsibilityExample {
    public static void main(String[] args) {
        Handler handlerA = new ConcreteHandlerA();
        Handler handlerB = new ConcreteHandlerB();
        
        handlerA.setNextHandler(handlerB);
        
        handlerA.handleRequest("A"); // Processed by A
        handlerA.handleRequest("B"); // Passed to B and processed
        handlerA.handleRequest("C"); // Not handled
    }
}
```

## Use Cases
- **Logging frameworks**: Logs are passed through multiple handlers (console, file, remote server, etc.).
- **Authentication and Authorization**: Requests pass through different security layers.
- **UI Event Handling**: Events are passed through a chain of listeners.

## Conclusion
The **Chain of Responsibility** pattern is useful for scenarios where multiple objects can handle a request dynamically. It enhances code maintainability by reducing direct dependencies between objects.

