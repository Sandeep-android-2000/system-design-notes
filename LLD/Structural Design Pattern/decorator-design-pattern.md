# Decorator Design Pattern - Pizza Example

## **What is the Decorator Pattern?**
The **Decorator Pattern** is a structural design pattern that allows you to dynamically add new behavior to objects without modifying their existing structure. It helps in extending the functionality of objects in a flexible way.

## **Real-Life Example: Pizza with Toppings**
Consider an online pizza ordering system. A base pizza can have multiple toppings, such as **Extra Cheese, Jalapeno, Olives, Pepperoni**, etc. Instead of creating multiple subclasses for every possible combination of toppings, we can use the **Decorator Pattern** to dynamically add toppings at runtime.

---

## **Implementation in Java**

### **Step 1: Create a Base Interface**
This interface represents a **Pizza** that all types of pizzas (base and decorated) will implement.

```java
// Component Interface
interface Pizza {
    String getDescription();
    double getCost();
}
```

### **Step 2: Create Concrete Implementations (Base Pizzas)**
These are basic pizza types that can be customized with toppings.

```java
// Concrete Component - Margherita Pizza
class MargheritaPizza implements Pizza {
    @Override
    public String getDescription() {
        return "Margherita Pizza";
    }

    @Override
    public double getCost() {
        return 200.00;
    }
}

// Concrete Component - Farmhouse Pizza
class FarmhousePizza implements Pizza {
    @Override
    public String getDescription() {
        return "Farmhouse Pizza";
    }

    @Override
    public double getCost() {
        return 250.00;
    }
}
```

### **Step 3: Create an Abstract Decorator**
The decorator class should implement the same interface as the component, so it can replace it dynamically.

```java
// Abstract Decorator
abstract class PizzaDecorator implements Pizza {
    protected Pizza pizza;

    public PizzaDecorator(Pizza pizza) {
        this.pizza = pizza;
    }

    @Override
    public String getDescription() {
        return pizza.getDescription();
    }

    @Override
    public double getCost() {
        return pizza.getCost();
    }
}
```

### **Step 4: Create Concrete Decorators (Toppings)**
These classes add toppings to the pizza dynamically.

```java
// Concrete Decorator - Extra Cheese
class ExtraCheese extends PizzaDecorator {
    public ExtraCheese(Pizza pizza) {
        super(pizza);
    }

    @Override
    public String getDescription() {
        return pizza.getDescription() + ", Extra Cheese";
    }

    @Override
    public double getCost() {
        return pizza.getCost() + 50.00; // Adding extra cheese cost
    }
}

// Concrete Decorator - Jalapeno
class Jalapeno extends PizzaDecorator {
    public Jalapeno(Pizza pizza) {
        super(pizza);
    }

    @Override
    public String getDescription() {
        return pizza.getDescription() + ", Jalapeno";
    }

    @Override
    public double getCost() {
        return pizza.getCost() + 40.00; // Adding Jalapeno cost
    }
}

// Concrete Decorator - Olives
class Olives extends PizzaDecorator {
    public Olives(Pizza pizza) {
        super(pizza);
    }

    @Override
    public String getDescription() {
        return pizza.getDescription() + ", Olives";
    }

    @Override
    public double getCost() {
        return pizza.getCost() + 30.00; // Adding Olives cost
    }
}
```

### **Step 5: Client Code**
Now, we can dynamically add toppings to our pizza.

```java
public class DecoratorPatternDemo {
    public static void main(String[] args) {
        // Order 1: Margherita Pizza with Extra Cheese and Jalapeno
        Pizza pizza1 = new MargheritaPizza();
        pizza1 = new ExtraCheese(pizza1);
        pizza1 = new Jalapeno(pizza1);
        System.out.println("Order 1: " + pizza1.getDescription());
        System.out.println("Total Cost: ₹" + pizza1.getCost());

        System.out.println("----------------------------------");

        // Order 2: Farmhouse Pizza with Extra Cheese, Jalapeno, and Olives
        Pizza pizza2 = new FarmhousePizza();
        pizza2 = new ExtraCheese(pizza2);
        pizza2 = new Jalapeno(pizza2);
        pizza2 = new Olives(pizza2);
        System.out.println("Order 2: " + pizza2.getDescription());
        System.out.println("Total Cost: ₹" + pizza2.getCost());
    }
}
```

---

## **Output**
```
Order 1: Margherita Pizza, Extra Cheese, Jalapeno
Total Cost: ₹290.0
----------------------------------
Order 2: Farmhouse Pizza, Extra Cheese, Jalapeno, Olives
Total Cost: ₹370.0
```

---

## **How Decorator Pattern Prevents Class Explosion?**
Without the **Decorator Pattern**, we would need to create multiple subclasses for every possible combination of pizzas and toppings:

- **MargheritaWithCheese**
- **MargheritaWithJalapeno**
- **MargheritaWithOlives**
- **MargheritaWithCheeseAndJalapeno**
- **MargheritaWithCheeseAndOlives**
- **FarmhouseWithCheese**
- **FarmhouseWithJalapeno**
- **FarmhouseWithCheeseAndJalapeno**, and so on...

This **"Class Explosion"** problem leads to unnecessary complexity and redundancy. The **Decorator Pattern** solves this by allowing dynamic extension of object behavior at runtime instead of creating separate subclasses.

---

## **Advantages of the Decorator Pattern**
✔ **Flexibility:** New toppings (features) can be added without modifying existing classes.  
✔ **Prevents Class Explosion:** No need to create multiple subclasses for every combination.  
✔ **Open-Closed Principle:** The base class remains unchanged, while new features can be added easily.  
✔ **Single Responsibility Principle:** Each decorator class has a single responsibility (e.g., adding cheese, adding jalapenos, etc.).

---

## **Conclusion**
The **Decorator Pattern** is a great way to extend an object's functionality dynamically. In our **Pizza Example**, we can keep adding toppings **without modifying the base class**. This makes the code more **scalable, maintainable, and reusable**.
