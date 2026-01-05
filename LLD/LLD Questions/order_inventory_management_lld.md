
# Order & Inventory Management System (Zepto-like) – Low Level Design

---

## 1. Problem Statement

Design a **Low Level Design (LLD)** for an **Order and Inventory Management System** similar to Zepto / Blinkit.

The system should allow:
- Users to browse products
- Add products to a cart
- Place an order
- Generate invoice
- Complete payment
- Update inventory
- Track order status

This design focuses **only on core business logic**.

---

## 2. Requirement Clarification

### Functional Requirements
- Multiple warehouses exist in different locations
- Each warehouse has its own inventory
- A single order must be fulfilled by **one warehouse only**
- Users add items by **category & quantity**
- Inventory must be updated after checkout
- Payment failure should rollback inventory
- Support multiple payment modes

### Non-Functional Requirements
- Extensible design
- Clean separation of concerns
- Interview-friendly and readable

### Out of Scope
- Authentication & authorization
- Notifications
- Delivery partner assignment
- Scalability & microservices

---

## 3. User / Happy Flow

1. User opens the app
2. System selects a warehouse
3. Inventory from selected warehouse is shown
4. User adds items to cart
5. User places order
6. Invoice is generated
7. User completes payment
8. Inventory is updated
9. Order status is updated

---

## 4. Entities

### 4.1 Product

```java
class Product {
    int id;
    String name;
}
```

---

### 4.2 ProductCategory

```java
class ProductCategory {
    int categoryId;
    String categoryName;
    List<Product> products;
    double price;
}
```

Why category-based?
- Same product shown once
- User selects quantity
- Same price for all items

---

### 4.3 Inventory

```java
class ProductInventory {
    List<ProductCategory> categories;
}
```

---

### 4.4 Warehouse

```java
class Warehouse {
    ProductInventory inventory;
    Address address;
}
```

---

### 4.5 Address

```java
class Address {
    int pincode;
    String city;
    String state;
}
```

---

### 4.6 User

```java
class User {
    int userId;
    String name;
    Cart cart;
    List<Integer> orderIds;
}
```

---

### 4.7 Cart

```java
class Cart {
    Map<Integer, Integer> categoryVsCount;
}
```

---

### 4.8 Order

```java
class Order {
    int orderId;
    User user;
    Address deliveryAddress;
    Warehouse warehouse;
    Map<Integer, Integer> categoryVsCount;
    Invoice invoice;
    Payment payment;
    OrderStatus status;
}
```

---

### 4.9 Invoice

```java
class Invoice {
    double itemTotal;
    double tax;
    double finalAmount;
}
```

---

### 4.10 Payment

```java
class Payment {
    PaymentStrategy strategy;
}
```

---

### 4.11 OrderStatus

```java
enum OrderStatus {
    PENDING,
    DELIVERED,
    UNDELIVERED
}
```

---

## 5. Relationships Between Entities

- User **has one** Cart
- User **has many** Orders (via order IDs)
- Warehouse **has one** Inventory
- Inventory **has many** ProductCategories
- ProductCategory **has many** Products
- Order **belongs to one** User
- Order **uses one** Warehouse
- Order **has one** Invoice
- Order **has one** Payment

---

## 6. Design Patterns Used

### Strategy Pattern
- Warehouse selection
- Payment modes

```java
interface WarehouseSelectionStrategy {
    Warehouse selectWarehouse(List<Warehouse> warehouses);
}
```

```java
interface PaymentStrategy {
    boolean makePayment(double amount);
}
```

---

## 7. Controllers

### UserController
- Manage users

### WarehouseController
- Manage warehouses
- Select warehouse using strategy

### OrderController
- Place orders
- Checkout
- Handle inventory updates

---

## 8. Java Code (Core Flow)

### Place Order (Simplified)

```java
Order placeOrder(User user, Warehouse warehouse, Address address) {
    Order order = new Order(user, warehouse, address);
    order.generateInvoice();
    return order;
}
```

---

## 9. Driver Code (`App.java`)

```java
public class App {

    public static void main(String[] args) {

        UserController userController = new UserController();
        WarehouseController warehouseController = new WarehouseController();
        OrderController orderController = new OrderController();

        Address warehouseAddress = new Address(560001, "Bangalore", "Karnataka");
        ProductInventory inventory = new ProductInventory();

        ProductCategory drinks =
                new ProductCategory(1, "Cold Drink", 100);
        drinks.addProduct(new Product(1, "Bottle"));
        drinks.addProduct(new Product(2, "Bottle"));

        inventory.addProductCategory(drinks);

        Warehouse warehouse = new Warehouse(inventory, warehouseAddress);
        warehouseController.addWarehouse(warehouse);
        warehouseController.setStrategy(new NearestWarehouseStrategy());

        User user = new User(1, "Sandeep",
                new Address(560003, "Bangalore", "Karnataka"));
        userController.addUser(user);

        Warehouse selectedWarehouse =
                warehouseController.selectWarehouse();

        user.getCart().addItem(1, 2);

        Order order = orderController.placeOrder(
                user,
                selectedWarehouse,
                user.getAddress()
        );

        Payment payment =
                new Payment(new UpiPaymentStrategy());

        orderController.checkout(order, payment);

        System.out.println("Order Status: " + order.getStatus());
    }
}
```

---

## 10. Summary

- Clean LLD for Order & Inventory systems
- Category-based cart simplifies logic
- Strategy pattern ensures extensibility
- App acts as driver / composition root
- Interview-ready and scalable design


---

## 11. UML Diagram

The following UML diagram represents the **complete structure and relationships**
between entities in the Order & Inventory Management System.

It highlights:
- Core domain entities (User, Order, Warehouse, Inventory, Product)
- Controller layer
- Strategy pattern usage (Warehouse selection, Payment)
- Ownership and associations (has-a relationships)

### UML Diagram Overview

- **User** has one **Cart** and multiple **Orders**
- **WarehouseController** manages multiple **Warehouses**
- **Warehouse** owns **Inventory**
- **Inventory** contains **ProductCategories**
- **ProductCategory** groups multiple **Products**
- **Order** aggregates User, Warehouse, Invoice, Payment
- **Strategy Pattern** used for:
  - Warehouse selection
  - Payment processing

### UML Diagram Image

![Order & Inventory Management UML](Order_Inventory Managment System.excalidraw.png)

---
