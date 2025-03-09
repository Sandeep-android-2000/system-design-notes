# Factory Pattern and Abstract Factory Pattern in System Design

## **1. Factory Design Pattern**
The **Factory Pattern** is a **creational design pattern** that provides an interface for creating objects but lets subclasses alter the type of objects that will be created. It encapsulates object creation logic to make code more maintainable and scalable.

### **Key Features**
- Provides a centralized object creation mechanism.
- Avoids tight coupling between client code and concrete classes.
- Enhances code maintainability and testability.

### **Implementation Example: Vehicle Factory**
```java
// Step 1: Create an interface
interface Vehicle {
    void drive();
}

// Step 2: Implement concrete classes
class Car implements Vehicle {
    @Override
    public void drive() {
        System.out.println("Driving a car...");
    }
}

class Bike implements Vehicle {
    @Override
    public void drive() {
        System.out.println("Riding a bike...");
    }
}

// Step 3: Create a Factory class
class VehicleFactory {
    public static Vehicle getVehicle(String type) {
        if (type.equalsIgnoreCase("car")) {
            return new Car();
        } else if (type.equalsIgnoreCase("bike")) {
            return new Bike();
        }
        return null;
    }
}

// Step 4: Client code
public class FactoryPatternDemo {
    public static void main(String[] args) {
        Vehicle vehicle1 = VehicleFactory.getVehicle("car");
        vehicle1.drive();

        Vehicle vehicle2 = VehicleFactory.getVehicle("bike");
        vehicle2.drive();
    }
}
```

### **Benefits of Factory Pattern**
✔ Encapsulates object creation logic.  
✔ Reduces dependencies between client code and concrete classes.  
✔ Simplifies code modification and extension.

---

## **2. Abstract Factory Pattern**
The **Abstract Factory Pattern** is an extension of the Factory Pattern that provides an interface for creating families of related or dependent objects **without specifying their concrete classes**.

### **Key Features**
- Used when multiple types of related objects need to be created.
- Provides a higher level of abstraction compared to the Factory Pattern.
- Helps maintain consistency when dealing with multiple object families.

### **Implementation Example: Abstract Factory for UI Components (Windows vs. MacOS)**
```java
// Step 1: Define Abstract Product Interfaces
interface Button {
    void render();
}

interface Checkbox {
    void render();
}

// Step 2: Implement Concrete Products for Windows
class WindowsButton implements Button {
    @Override
    public void render() {
        System.out.println("Rendering Windows Button");
    }
}

class WindowsCheckbox implements Checkbox {
    @Override
    public void render() {
        System.out.println("Rendering Windows Checkbox");
    }
}

// Step 3: Implement Concrete Products for MacOS
class MacButton implements Button {
    @Override
    public void render() {
        System.out.println("Rendering Mac Button");
    }
}

class MacCheckbox implements Checkbox {
    @Override
    public void render() {
        System.out.println("Rendering Mac Checkbox");
    }
}

// Step 4: Create Abstract Factory Interface
interface GUIFactory {
    Button createButton();
    Checkbox createCheckbox();
}

// Step 5: Implement Concrete Factories
class WindowsFactory implements GUIFactory {
    @Override
    public Button createButton() {
        return new WindowsButton();
    }

    @Override
    public Checkbox createCheckbox() {
        return new WindowsCheckbox();
    }
}

class MacFactory implements GUIFactory {
    @Override
    public Button createButton() {
        return new MacButton();
    }

    @Override
    public Checkbox createCheckbox() {
        return new MacCheckbox();
    }
}

// Step 6: Client Code
public class AbstractFactoryPatternDemo {
    public static GUIFactory getFactory(String osType) {
        if (osType.equalsIgnoreCase("windows")) {
            return new WindowsFactory();
        } else if (osType.equalsIgnoreCase("mac")) {
            return new MacFactory();
        }
        return null;
    }

    public static void main(String[] args) {
        GUIFactory factory = getFactory("windows");

        Button button = factory.createButton();
        button.render();

        Checkbox checkbox = factory.createCheckbox();
        checkbox.render();
    }
}
```

### **Benefits of Abstract Factory Pattern**
✔ Ensures consistency across object families.  
✔ Reduces dependency on concrete classes.  
✔ Supports scalability and extension.

---

### **Factory vs. Abstract Factory Pattern**
| Feature | Factory Pattern | Abstract Factory Pattern |
|---------|----------------|--------------------------|
| Use Case | Creating a single type of object | Creating families of related objects |
| Abstraction Level | Lower | Higher |
| Object Families | Not considered | Maintains consistency within object families |
| Example | Vehicle Factory (Car, Bike) | UI Factory (Windows, MacOS) |

Both patterns help manage object creation efficiently while maintaining flexibility and extensibility in system design.
