# Factory Pattern and Abstract Factory Pattern

## **1. Factory Pattern**

### **What is the Factory Pattern?**
The **Factory Pattern** is a creational design pattern that provides an interface for creating objects but lets subclasses alter the type of objects that will be created. It **hides the object creation logic** and allows flexibility in adding new types.

### **Real-Life Example: Shapes Factory**
Suppose we need to create different types of **shapes** like **Circle, Rectangle, and Square**. Instead of using `new` keyword everywhere, we use a **Shape Factory** to create instances dynamically.

---

## **Implementation in Java**

### **Step 1: Create a Shape Interface**

```java
// Shape Interface
interface Shape {
    void draw();
}
```

### **Step 2: Create Concrete Implementations**

```java
// Circle Implementation
class Circle implements Shape {
    @Override
    public void draw() {
        System.out.println("Drawing a Circle.");
    }
}

// Rectangle Implementation
class Rectangle implements Shape {
    @Override
    public void draw() {
        System.out.println("Drawing a Rectangle.");
    }
}

// Square Implementation
class Square implements Shape {
    @Override
    public void draw() {
        System.out.println("Drawing a Square.");
    }
}
```

### **Step 3: Create a Factory Class**

```java
// Shape Factory
class ShapeFactory {
    // Factory Method
    public Shape getShape(String shapeType) {
        if (shapeType == null) {
            return null;
        }
        if (shapeType.equalsIgnoreCase("CIRCLE")) {
            return new Circle();
        } else if (shapeType.equalsIgnoreCase("RECTANGLE")) {
            return new Rectangle();
        } else if (shapeType.equalsIgnoreCase("SQUARE")) {
            return new Square();
        }
        return null;
    }
}
```

### **Step 4: Client Code**

```java
public class FactoryPatternDemo {
    public static void main(String[] args) {
        ShapeFactory shapeFactory = new ShapeFactory();

        // Get an object of Circle and call its draw method
        Shape shape1 = shapeFactory.getShape("CIRCLE");
        shape1.draw();

        // Get an object of Rectangle and call its draw method
        Shape shape2 = shapeFactory.getShape("RECTANGLE");
        shape2.draw();

        // Get an object of Square and call its draw method
        Shape shape3 = shapeFactory.getShape("SQUARE");
        shape3.draw();
    }
}
```

---

## **Output**
```
Drawing a Circle.
Drawing a Rectangle.
Drawing a Square.
```

---

## **When to Use the Factory Pattern?**
- When the exact type of object **isn't known until runtime**.
- When we want to **hide object creation complexity**.
- When we need **better maintainability and scalability**.

---

## **2. Abstract Factory Pattern**

### **What is the Abstract Factory Pattern?**
The **Abstract Factory Pattern** is an extension of the **Factory Pattern** that provides an interface for creating **families of related objects** without specifying their concrete classes. 

### **Real-Life Example: Vehicle Factory**
We categorize **vehicles** into:
1. **Luxury Vehicles** - Luxury Car, Luxury Bike.
2. **Ordinary Vehicles** - Ordinary Car, Ordinary Bike.

Each category consists of **cars** and **bikes**, and we use an **Abstract Factory** to manage them.

---

## **Implementation in Java**

### **Step 1: Create Interfaces for Vehicle Types**

```java
// Car Interface
interface Car {
    void drive();
}

// Bike Interface
interface Bike {
    void ride();
}
```

### **Step 2: Create Concrete Implementations**

```java
// Luxury Car Implementation
class LuxuryCar implements Car {
    @Override
    public void drive() {
        System.out.println("Driving a luxury car with premium features.");
    }
}

// Ordinary Car Implementation
class OrdinaryCar implements Car {
    @Override
    public void drive() {
        System.out.println("Driving an ordinary car with basic features.");
    }
}

// Luxury Bike Implementation
class LuxuryBike implements Bike {
    @Override
    public void ride() {
        System.out.println("Riding a luxury bike with advanced features.");
    }
}

// Ordinary Bike Implementation
class OrdinaryBike implements Bike {
    @Override
    public void ride() {
        System.out.println("Riding an ordinary bike with standard features.");
    }
}
```

### **Step 3: Create an Abstract Factory**

```java
// Abstract Factory
interface VehicleFactory {
    Car createCar();
    Bike createBike();
}
```

### **Step 4: Create Concrete Factories**

```java
// Factory for Luxury Vehicles
class LuxuryVehicleFactory implements VehicleFactory {
    @Override
    public Car createCar() {
        return new LuxuryCar();
    }

    @Override
    public Bike createBike() {
        return new LuxuryBike();
    }
}

// Factory for Ordinary Vehicles
class OrdinaryVehicleFactory implements VehicleFactory {
    @Override
    public Car createCar() {
        return new OrdinaryCar();
    }

    @Override
    public Bike createBike() {
        return new OrdinaryBike();
    }
}
```

### **Step 5: Factory Producer**

```java
// Factory Producer
class FactoryProducer {
    public static VehicleFactory getFactory(String vehicleType) {
        if (vehicleType.equalsIgnoreCase("Luxury")) {
            return new LuxuryVehicleFactory();
        } else if (vehicleType.equalsIgnoreCase("Ordinary")) {
            return new OrdinaryVehicleFactory();
        }
        return null;
    }
}
```

### **Step 6: Client Code**

```java
public class AbstractFactoryPatternDemo {
    public static void main(String[] args) {
        // Get Luxury Vehicle Factory
        VehicleFactory luxuryFactory = FactoryProducer.getFactory("Luxury");
        Car luxuryCar = luxuryFactory.createCar();
        Bike luxuryBike = luxuryFactory.createBike();
        
        luxuryCar.drive();
        luxuryBike.ride();

        System.out.println("----------------------------------");

        // Get Ordinary Vehicle Factory
        VehicleFactory ordinaryFactory = FactoryProducer.getFactory("Ordinary");
        Car ordinaryCar = ordinaryFactory.createCar();
        Bike ordinaryBike = ordinaryFactory.createBike();
        
        ordinaryCar.drive();
        ordinaryBike.ride();
    }
}
```

---

## **Output**
```
Driving a luxury car with premium features.
Riding a luxury bike with advanced features.
----------------------------------
Driving an ordinary car with basic features.
Riding an ordinary bike with standard features.
```

---

## **Factory vs Abstract Factory Pattern**

| Feature                 | Factory Pattern              | Abstract Factory Pattern |
|-------------------------|-----------------------------|--------------------------|
| **Purpose**            | Creates objects of a single family | Creates **families** of related objects |
| **Example**            | Shape Factory (Circle, Square, Rectangle) | Vehicle Factory (Luxury & Ordinary) |
| **Scalability**        | Adding new object types requires modifying the factory | Adding new families is easy |
| **Encapsulation**      | Encapsulates object creation for a single type | Encapsulates object creation for multiple types |

---

## **Conclusion**
- **Factory Pattern** is useful when we need to instantiate objects **of a single category** dynamically.
- **Abstract Factory Pattern** is useful when we need to create **multiple families of related objects**, ensuring consistency and scalability.

These patterns **enhance modularity**, **reduce tight coupling**, and **improve maintainability** in large software systems.

