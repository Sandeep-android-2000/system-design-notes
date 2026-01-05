
# Order & Inventory Management System (Zepto-like) – Low Level Design

---

## 1. Problem Statement

Design a Low Level Design (LLD) for an Order & Inventory Management System similar to Zepto.
The system should support product browsing, cart, order placement, invoice generation,
payment, and inventory updates.

---

## 2. Requirement Clarification

### Functional Requirements
- Multiple warehouses
- Each order fulfilled by a single warehouse
- Category-based product selection
- Inventory update on checkout
- Payment handling
- Order lifecycle management

### Non-Functional Requirements
- Simple and readable design
- Extensible
- Interview-friendly

---

## 3. User / Happy Flow

1. User opens app
2. Warehouse selected
3. Inventory shown
4. User adds items to cart
5. User places order
6. Invoice generated
7. Payment done
8. Inventory updated
9. Order status updated

---

## 4. Entities (Complete Java Code)

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

## 5. Cart & User

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

## 6. Order, Invoice, Payment

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

### Payment

```java
interface PaymentStrategy {
    boolean pay(double amount);
}

class UpiPayment implements PaymentStrategy {
    public boolean pay(double amount) {
        return true; // success
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

## 7. Controllers

---

### OrderController

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

        // reduce inventory
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

## 8. Driver Code (App)

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

        User user =
            new User(1, "Sandeep",
                new Address(560003, "Bangalore", "KA"));

        user.cart.addItem(1, 2);

        OrderController oc = new OrderController();
        Order order = oc.placeOrder(user, warehouse);

        oc.checkout(order, new UpiPayment());

        System.out.println("Order Status: " + order.status);
        System.out.println("Remaining Stock: "
            + drinks.availableCount());
    }
}
```

---

## 9. UML Diagram

<img width="6371" height="4723" alt="Order_Inventory Managment System excalidraw" src="https://github.com/user-attachments/assets/04e1fe37-0147-4540-a832-1da214e6248b" />


---

## 10. Summary

- Fully working simple Java design
- Easy to explain in interviews
- Clear inventory & order flow
- Strategy pattern used for payment
