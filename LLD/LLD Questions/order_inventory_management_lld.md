
# Order & Inventory Management System (Zepto-like) – Low Level Design

---

## 1. Problem Statement

Design a Low Level Design (LLD) for an Order & Inventory Management System similar to Zepto.
The system should support product browsing, cart, order placement, invoice generation,
payment, warehouse-based inventory management, and order lifecycle handling.

---

## 2. Requirement Clarification

### Functional Requirements
- Multiple warehouses in different locations
- Each order must be fulfilled by exactly **one warehouse**
- Warehouse is selected dynamically using a strategy
- Category-based product selection (quantity driven)
- Inventory updates on checkout
- Payment handled via multiple payment modes
- Order status management

### Non-Functional Requirements
- Simple, readable Java code
- Easy to explain in interviews
- Extensible design (Open for extension)

---

## 3. User / Happy Flow

1. User opens the app
2. System selects a warehouse using a strategy
3. Inventory of selected warehouse is shown
4. User adds items to cart
5. User places order
6. Invoice is generated
7. Payment is made
8. Inventory is updated
9. Order status updated

---

## 4. Core Entities (Complete Java Code)

---

### Address

```java
class Address {
    int pincode;
    String city;
    String state;

    Address(int pincode, String city, String state) {
        this.pincode = pincode;
        this.city = city;
        this.state = state;
    }
}
```

---

### Product

```java
class Product {
    int id;
    String name;

    Product(int id, String name) {
        this.id = id;
        this.name = name;
    }
}
```

---

### ProductCategory

```java
import java.util.*;

class ProductCategory {
    int categoryId;
    String name;
    double price;
    List<Product> products = new ArrayList<>();

    ProductCategory(int categoryId, String name, double price) {
        this.categoryId = categoryId;
        this.name = name;
        this.price = price;
    }

    void addProduct(Product product) {
        products.add(product);
    }

    void removeProducts(int count) {
        for (int i = 0; i < count && !products.isEmpty(); i++) {
            products.remove(products.size() - 1);
        }
    }

    int availableCount() {
        return products.size();
    }
}
```

---

### Inventory

```java
class Inventory {
    Map<Integer, ProductCategory> categories = new HashMap<>();

    void addCategory(ProductCategory category) {
        categories.put(category.categoryId, category);
    }

    ProductCategory getCategory(int categoryId) {
        return categories.get(categoryId);
    }
}
```

---

### Warehouse

```java
class Warehouse {
    Inventory inventory;
    Address address;

    Warehouse(Inventory inventory, Address address) {
        this.inventory = inventory;
        this.address = address;
    }
}
```

---

## 5. Warehouse Selection Strategy (Strategy Pattern)

---

### WarehouseSelectionStrategy

```java
import java.util.List;

interface WarehouseSelectionStrategy {
    Warehouse selectWarehouse(List<Warehouse> warehouses, Address userAddress);
}
```

---

### NearestWarehouseStrategy (Simple Implementation)

```java
class NearestWarehouseStrategy implements WarehouseSelectionStrategy {

    @Override
    public Warehouse selectWarehouse(List<Warehouse> warehouses, Address userAddress) {
        // Simplified logic: return first warehouse
        return warehouses.get(0);
    }
}
```

---

### WarehouseController

```java
class WarehouseController {

    List<Warehouse> warehouses = new ArrayList<>();
    WarehouseSelectionStrategy strategy;

    void addWarehouse(Warehouse warehouse) {
        warehouses.add(warehouse);
    }

    void setStrategy(WarehouseSelectionStrategy strategy) {
        this.strategy = strategy;
    }

    Warehouse selectWarehouse(Address userAddress) {
        return strategy.selectWarehouse(warehouses, userAddress);
    }
}
```

---

## 6. Cart & User

---

### Cart

```java
class Cart {
    Map<Integer, Integer> items = new HashMap<>();

    void addItem(int categoryId, int count) {
        items.put(categoryId, items.getOrDefault(categoryId, 0) + count);
    }

    Map<Integer, Integer> getItems() {
        return items;
    }

    void clear() {
        items.clear();
    }
}
```

---

### User

```java
class User {
    int id;
    String name;
    Address address;
    Cart cart = new Cart();

    User(int id, String name, Address address) {
        this.id = id;
        this.name = name;
        this.address = address;
    }
}
```

---

## 7. Order, Invoice & Payment

---

### OrderStatus

```java
enum OrderStatus {
    CREATED, PAID, CANCELLED
}
```

---

### Invoice

```java
class Invoice {
    double itemTotal;
    double tax;
    double finalAmount;

    Invoice(double itemTotal) {
        this.itemTotal = itemTotal;
        this.tax = itemTotal * 0.05;
        this.finalAmount = itemTotal + tax;
    }
}
```

---

## 8. Payment Strategy (Strategy Pattern)

---

### PaymentStrategy

```java
interface PaymentStrategy {
    boolean pay(double amount);
}
```

---

### UpiPaymentStrategy

```java
class UpiPaymentStrategy implements PaymentStrategy {

    @Override
    public boolean pay(double amount) {
        // Simulate successful payment
        return true;
    }
}
```

---

### CardPaymentStrategy

```java
class CardPaymentStrategy implements PaymentStrategy {

    @Override
    public boolean pay(double amount) {
        // Simulate successful payment
        return true;
    }
}
```

---

### Order

```java
class Order {
    static int counter = 1;
    int orderId;
    User user;
    Warehouse warehouse;
    Map<Integer, Integer> items;
    Invoice invoice;
    OrderStatus status;

    Order(User user, Warehouse warehouse, Map<Integer, Integer> items) {
        this.orderId = counter++;
        this.user = user;
        this.warehouse = warehouse;
        this.items = new HashMap<>(items);
        this.status = OrderStatus.CREATED;
    }
}
```

---

## 9. OrderController

```java
class OrderController {

    Order placeOrder(User user, Warehouse warehouse) {

        Order order = new Order(user, warehouse, user.cart.getItems());

        double total = 0;
        for (Map.Entry<Integer, Integer> entry : order.items.entrySet()) {
            ProductCategory category =
                warehouse.inventory.getCategory(entry.getKey());
            total += category.price * entry.getValue();
        }

        order.invoice = new Invoice(total);
        return order;
    }

    void checkout(Order order, PaymentStrategy payment) {

        // Reduce inventory
        for (Map.Entry<Integer, Integer> entry : order.items.entrySet()) {
            ProductCategory category =
                order.warehouse.inventory.getCategory(entry.getKey());
            category.removeProducts(entry.getValue());
        }

        boolean success = payment.pay(order.invoice.finalAmount);

        if (success) {
            order.status = OrderStatus.PAID;
            order.user.cart.clear();
        } else {
            order.status = OrderStatus.CANCELLED;
        }
    }
}
```

---

## 10. Driver Code (App)

```java
public class App {

    public static void main(String[] args) {

        Inventory inventory = new Inventory();

        ProductCategory drinks =
            new ProductCategory(1, "Cold Drink", 100);
        drinks.addProduct(new Product(1, "Bottle"));
        drinks.addProduct(new Product(2, "Bottle"));

        inventory.addCategory(drinks);

        Warehouse warehouse =
            new Warehouse(inventory,
                new Address(560001, "Bangalore", "KA"));

        WarehouseController warehouseController = new WarehouseController();
        warehouseController.addWarehouse(warehouse);
        warehouseController.setStrategy(new NearestWarehouseStrategy());

        User user =
            new User(1, "Sandeep",
                new Address(560003, "Bangalore", "KA"));

        Warehouse selectedWarehouse =
            warehouseController.selectWarehouse(user.address);

        user.cart.addItem(1, 2);

        OrderController oc = new OrderController();
        Order order = oc.placeOrder(user, selectedWarehouse);

        oc.checkout(order, new UpiPaymentStrategy());

        System.out.println("Order Status: " + order.status);
        System.out.println("Remaining Stock: "
            + drinks.availableCount());
    }
}
```

---

## 11. UML Diagram

![UML Diagram](Order_Inventory Managment System.excalidraw.png)

---

## 12. Summary

- Complete working Java code
- Warehouse selection via Strategy pattern
- Payment via Strategy pattern
- Simple, interview-ready LLD
- Easy to extend (new strategies, coupons, etc.)
